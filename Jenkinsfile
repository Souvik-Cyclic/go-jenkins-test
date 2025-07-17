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
                        echo 'Starting Docker build...'
                        // Capture both stdout and stderr
                        output = sh(script: "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} . 2>&1", returnStdout: true).trim()
                        echo 'Docker build completed.'
                    } catch (err) {
                        output = err.getMessage()
                        success = false
                    }

                    def cleanOutput = output ?: "⚠️ No output captured from docker build."
                    cleanOutput = cleanOutput.replaceAll('```', '---') // Sanitize Markdown backticks

                    // Publish GitHub check with build output
                    publishChecks(
                        name: 'Docker Build',
                        title: 'Docker Build Result',
                        summary: success ? '✅ Build succeeded' : '❌ Build failed',
                        text: """
### Docker Build Output

\`\`\`
${cleanOutput.take(60000)}
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
