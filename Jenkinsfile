pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:$PATH"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    def testStatus = true
                    try {
                        sh 'npm test'
                    } catch (err) {
                        testStatus = false
                        currentBuild.result = 'FAILURE'
                    } finally {
                        emailext(
                            to: 'riteshagarwal.au@gmail.com',
                            subject: "Jenkins Test Stage - ${testStatus ? 'SUCCESS' : 'FAILURE'}",
                            body: """<p>The 'Run Tests' stage has ${testStatus ? 'succeeded' : 'failed'}.</p>
                                     <p>Check Jenkins logs for details.</p>""",
                            attachLog: true
                        )
                    }
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    def auditStatus = true
                    try {
                        sh 'npm audit'
                    } catch (err) {
                        auditStatus = false
                        currentBuild.result = 'FAILURE'
                    } finally {
                        emailext(
                            to: 'riteshagarwal.au@gmail.com',
                            subject: "Jenkins Security Scan Stage - ${auditStatus ? 'SUCCESS' : 'FAILURE'}",
                            body: """<p>The 'Security Scan' stage has ${auditStatus ? 'succeeded' : 'failed'}.</p>
                                     <p>Check Jenkins logs for details.</p>""",
                            attachLog: true
                        )
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh 'sonar-scanner -Dsonar.login=$SONAR_TOKEN'
                }
            }
        }
    }
}