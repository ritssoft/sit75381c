pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Allows pipeline to continue despite test failures
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                // Show known CVEs in the output
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm audit || true'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh 'export PATH=/opt/homebrew/bin:$PATH && sonar-scanner -Dsonar.login=$SONAR_TOKEN'
                }
            }
    }
}