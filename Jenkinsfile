pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-app-image"
        DOCKER_REGISTRY = "your-dockerhub-username"
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
                script {
                    echo "Building Docker image..."
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    echo "Testing Docker image..."
                    sh "docker run --rm ${IMAGE_NAME}:latest python -m unittest || true"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    echo "Logging in to DockerHub..."
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                    sh "docker tag ${IMAGE_NAME}:latest ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest"
                    sh "docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Cleanup') {
            steps {
                echo "Cleaning up local images..."
                sh "docker rmi ${IMAGE_NAME}:latest || true"
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
