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
                    def result = ''
                    def success = true
                    try {
                        result = sh(script: "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .", returnStdout: true).trim()
                    } catch (e) {
                        result = e.getMessage()
                        success = false
                    }

                    // Publish the GitHub Check
                    githubChecks name: 'Docker Build', summary: success ? 'Build successful ✅' : 'Build failed ❌', text: "```\n${result.take(60000)}\n```", status: 'COMPLETED', conclusion: success ? 'SUCCESS' : 'FAILURE'

                    if (!success) {
                        error("Docker build failed")
                    }
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
