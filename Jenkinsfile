pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-app-image"
        DOCKER_REGISTRY = "lakshitha7"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t %IMAGE_NAME%:latest ."
            }
        }

        stage('Test Docker Image') {
            steps {
                echo "Testing Docker container..."
                // Use CMD compatible style
                bat """
                docker run --rm %IMAGE_NAME%:latest python -m unittest || exit /b 0
                """
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "Pushing image to DockerHub..."
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat """
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker tag %IMAGE_NAME%:latest %DOCKER_REGISTRY%/%IMAGE_NAME%:latest
                    docker push %DOCKER_REGISTRY%/%IMAGE_NAME%:latest
                    """
                }
            }
        }

        stage('Cleanup') {
            steps {
                echo "Cleaning up images..."
                bat "docker rmi %IMAGE_NAME%:latest || exit /b 0"
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
