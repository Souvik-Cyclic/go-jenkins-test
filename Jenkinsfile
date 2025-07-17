pipeline {
    agent any

    environment {
        IMAGE_NAME = "go-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up Docker image...'
            script {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
            }
        }
        success {
            echo '✅ Build succeeded!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
