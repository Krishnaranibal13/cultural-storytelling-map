
pipeline {

    agent any

    environment {
        COMPOSE_PROJECT_NAME = "cultural-storytelling"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm

                echo "========================================="
                echo "Checkout completed"
                echo "Jenkins Workspace: ${env.WORKSPACE}"
                echo "========================================="
            }
        }

        stage('Verify Project') {
            steps {
                sh '''
                    set -e

                    echo "Checking required project files..."

                    test -f "${WORKSPACE}/docker-compose.yml"
                    test -f "${WORKSPACE}/backend/Dockerfile"
                    test -f "${WORKSPACE}/frontend/Dockerfile"
                    test -f "${WORKSPACE}/nginx/nginx.conf"

                    echo "All required project files found."
                '''
            }
        }

        stage('Create Backend Environment') {
            steps {

                withCredentials([
                    file(
                        credentialsId: 'cultural-storytelling-env',
                        variable: 'BACKEND_ENV_FILE'
                    )
                ]) {

                    sh '''
                        set -e

                        echo "Creating backend environment file..."

                        cp "$BACKEND_ENV_FILE" "${WORKSPACE}/backend/.env"

                        chmod 600 "${WORKSPACE}/backend/.env"

                        echo "Backend .env created successfully."
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    set -e

                    cd "${WORKSPACE}"

                    echo "========================================="
                    echo "Building Docker images"
                    echo "========================================="

                    docker compose build
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    set -e

                    cd "${WORKSPACE}"

                    echo "========================================="
                    echo "Stopping old containers"
                    echo "========================================="

                    docker compose down

                    echo "========================================="
                    echo "Starting new containers"
                    echo "========================================="

                    docker compose up -d

                    echo "Containers started successfully."
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                    set -e

                    cd "${WORKSPACE}"

                    echo "========================================="
                    echo "Waiting for containers"
                    echo "========================================="

                    sleep 10

                    echo "========================================="
                    echo "Docker Compose Status"
                    echo "========================================="

                    docker compose ps

                    echo "========================================="
                    echo "Testing API"
                    echo "========================================="

                    curl -f http://localhost/api/stories

                    echo ""
                    echo "API is responding successfully."
                '''
            }
        }
    }

    post {

        success {
            echo "========================================="
            echo "DEPLOYMENT SUCCESSFUL"
            echo "========================================="
            echo "Build Number : ${env.BUILD_NUMBER}"
            echo "Workspace    : ${env.WORKSPACE}"
            echo "Application  : http://13.221.119.26"
            echo "========================================="
        }

        failure {
            echo "========================================="
            echo "DEPLOYMENT FAILED"
            echo "========================================="
            echo "Build Number : ${env.BUILD_NUMBER}"
            echo "Workspace    : ${env.WORKSPACE}"
            echo "========================================="

            sh '''
                cd "${WORKSPACE}" || exit 0

                echo "========================================="
    }
}
                echo "Docker Compose Status"
                echo "========================================="

        }
                docker compose ps || true

                echo "========================================="
            echo "Jenkins build completed: ${env.BUILD_NUMBER}"
        always {
                echo "Backend Logs"
                echo "========================================="


                docker compose logs --tail=50 backend || true
        }

            '''

                docker compose logs --tail=50 nginx || true
                echo "========================================="
                echo "========================================="
                echo "Nginx Logs"
                echo "Frontend Logs"
                echo "========================================="
                echo "========================================="



