import org.jenkinsci.plugins.github.checks.*

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
                    def logOutput = ''
                    try {
                        logOutput = sh(script: "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .", returnStdout: true)
                        publishChecks("Build Docker Image", logOutput, true)
                    } catch (err) {
                        logOutput = err.getMessage()
                        publishChecks("Build Docker Image", logOutput, false)
                        error "Build failed"
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

// Publish GitHub Checks (no library required)
def publishChecks(String title, String logText, boolean isSuccess) {
    def checksDetails = new ChecksDetails()
    checksDetails.setName("Jenkins Build Log")
    checksDetails.setStatus(ChecksStatus.COMPLETED)
    checksDetails.setConclusion(isSuccess ? ChecksConclusion.SUCCESS : ChecksConclusion.FAILURE)
    checksDetails.setOutput(new ChecksOutput(title, "```${logText.take(60000)}```"))

    def publisher = new ChecksPublisher()
    publisher.publish(checksDetails)
}
