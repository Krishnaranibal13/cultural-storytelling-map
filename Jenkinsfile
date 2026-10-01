
pipeline {

    agent any

    environment {
        COMPOSE_PROJECT_NAME = "cultural-storytelling"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm

                echo "Jenkins Workspace: ${env.WORKSPACE}"
                echo "Project Directory: ${pwd()}"
            }
        }

        stage('Verify Project') {
            steps {
                sh '''
                    set -e

                    echo "Checking project files..."

                    test -f "${WORKSPACE}/docker-compose.yml"
                    test -f "${WORKSPACE}/backend/Dockerfile"
                    test -f "${WORKSPACE}/frontend/Dockerfile"
                    test -f "${WORKSPACE}/nginx/nginx.conf"

                    echo "Required files found."
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    set -e

                    cd "${WORKSPACE}"

                    echo "Building Docker images..."

                    docker compose build
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    set -e

                    cd "${WORKSPACE}"

                    echo "Stopping old containers..."

                    docker compose down

                    echo "Starting application..."

                    docker compose up -d

                    echo "Deployment completed."
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                    set -e

                    cd "${WORKSPACE}"

                    echo "Waiting for containers..."
                    sleep 10

                    echo "Container status:"
                    docker compose ps

                    echo "Testing backend through Nginx..."

                    curl -f http://localhost/api/stories

                    echo ""
                    echo "Application is responding successfully."
                '''
            }
        }
    }

    post {

        success {
            echo "========================================="
            echo "Deployment Successful"
            echo "Workspace: ${env.WORKSPACE}"
            echo "Application: http://13.221.119.26"
            echo "========================================="
        }

        failure {
            echo "========================================="
            echo "Deployment Failed"
            echo "Workspace: ${env.WORKSPACE}"
            echo "Showing container status and logs..."
            echo "========================================="

            sh '''
                cd "${WORKSPACE}" || exit 0

                docker compose ps || true

                echo "----- Backend Logs -----"
                docker compose logs --tail=50 backend || true

                echo "----- Frontend Logs -----"
                docker compose logs --tail=50 frontend || true

                echo "----- Nginx Logs -----"
                docker compose logs --tail=50 nginx || true
            '''
        }

        always {
            echo "Jenkins build completed: ${env.BUILD_NUMBER}"
        }
    }
}

