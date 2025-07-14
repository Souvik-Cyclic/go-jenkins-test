pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'go version'
                sh 'go build -o app .'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'rm -f app'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}