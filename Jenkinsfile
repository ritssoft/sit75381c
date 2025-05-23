pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:$PATH"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm install'
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    def testStatus = true
                    try {
                        sh 'export PATH=/opt/homebrew/bin:$PATH && npm test'
                    } catch (err) {
                        testStatus = false
                        currentBuild.result = 'FAILURE'
                    } finally {
                        emailext(
                            to: 'riteshagarwal.au@gmail.com',
                            subject: "Jenkins Test Stage - ${testStatus ? 'SUCCESS' : 'FAILURE'}",
                            body: """<p>The 'Run Tests' stage has ${testStatus ? 'succeeded' : 'failed'}.</p>
                                     <p>Check Jenkins logs for details.</p>""",
                            mimeType: 'text/html',
                            attachLog: true
                        )
                    }
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    def auditStatus = true
                    try {
                        sh 'export PATH=/opt/homebrew/bin:$PATH && npm audit'
                    } catch (err) {
                        auditStatus = false
                        currentBuild.result = 'FAILURE'
                    } finally {
                        emailext(
                            to: 'riteshagarwal.au@gmail.com',
                            subject: "Jenkins Security Scan Stage - ${auditStatus ? 'SUCCESS' : 'FAILURE'}",
                            body: """<p>The 'Security Scan' stage has ${auditStatus ? 'succeeded' : 'failed'}.</p>
                                     <p>Check Jenkins logs for details.</p>""",
                            mimeType: 'text/html',
                            attachLog: true
                        )
                    }
                }
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
}