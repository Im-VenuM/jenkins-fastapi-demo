pipeline {
    agent any

    environment {
        IMAGE_NAME = "fastapi-jenkins-demo"
        CONTAINER_NAME = "fastapi-app"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    echo "Running FastAPI container..."
                    // Stop and remove old container if it exists
                    sh """
                        docker rm -f ${CONTAINER_NAME} || true
                        docker run -d --name ${CONTAINER_NAME} -p 8000:8000 ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Test App') {
            steps {
                script {
                    echo "Testing app availability..."
                    // Simple health check
                    sh "sleep 5 && curl -f http://localhost:8000 || exit 1"
                }
            }
        }

        stage('Cleanup (Optional)') {
            steps {
                echo "Cleaning up Docker resources..."
                sh "docker system prune -f || true"
            }
        }
    }

    post {
        always {
            echo "Pipeline completed. Stopping running containers."
        }
        failure {
            echo "Build failed!"
        }
        success {
            echo "FastAPI container is running successfully on port 8000."
        }
    }
}
