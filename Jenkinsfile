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
                    def output = ''
                    def success = true
                    try {
                        output = sh(script: "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .", returnStdout: true).trim()
                    } catch (err) {
                        output = err.getMessage()
                        success = false
                    }

                    // Sanitize output and provide fallback if empty
                    def cleanOutput = output ?: "⚠️ No output captured from docker build."

                    // Escape backticks if any
                    cleanOutput = cleanOutput.replaceAll('```', '\\`\\`\\`')

                    publishChecks(
                        name: 'Docker Build',
                        title: 'Docker Build Result',
                        summary: success ? '✅ Build succeeded' : '❌ Build failed',
                        text: """### Docker Build Output

\`\`\`
${cleanOutput}
\`\`\`
""",
                        conclusion: success ? 'SUCCESS' : 'FAILURE',
                        detailsURL: "${env.BUILD_URL}"
                    )

                    if (!success) {
                        error("Build failed")
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
        }
    }
}
