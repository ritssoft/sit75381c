pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ritssoft/sit75381c.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'export PATH=$PATH:/opt/homebrew/bin && npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Allows pipeline to continue despite test failures
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                // This will show known CVEs in the output
                sh 'npm audit || true'
            }
        }
    }
}