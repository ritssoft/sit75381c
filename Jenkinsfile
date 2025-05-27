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
                    withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                        try {
                            echo "DEBUG: Starting 'Run Tests' stage vulnerability scan..."
                            // Pass the token to Snyk for auth and run test
                            sh '''
                                export PATH=/opt/homebrew/bin:$PATH
                                export SNYK_TOKEN=$SNYK_TOKEN
                                snyk auth $SNYK_TOKEN
                                npm test
                            '''
                            echo "DEBUG: Snyk test command completed successfully."
                        } catch (err) {
                            testStatus = false
                            currentBuild.result = 'FAILURE'
                            echo "ERROR: Snyk test command failed with error: ${err}"
                        } finally {
                            echo "DEBUG: Entering 'Run Tests' finally block. testStatus=${testStatus}"
                            // Ensure Email Extension Plugin is installed and configured globally
                            // for 'Extended E-mail Notification' (SMTP settings, credentials, etc.)
                            try {
                                emailext(
                                    to: 'riteshagarwal.au@gmail.com',
                                    subject: "Jenkins Test Stage - ${testStatus ? 'SUCCESS' : 'FAILURE'} - ${env.JOB_NAME} #${env.BUILD_NUMBER}", // Added job and build info
                                    body: """<p>The 'Run Tests' stage for job <b>${env.JOB_NAME}</b> build <b>#${env.BUILD_NUMBER}</b> has ${testStatus ? 'succeeded' : 'failed'}.</p>
                                            <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                                            <p>Check Jenkins logs for details.</p>""",
                                    mimeType: 'text/html',
                                    attachLog: true // Attach entire console log
                                )
                                echo "DEBUG: Email for 'Run Tests' stage sent successfully from pipeline."
                            } catch (emailErr) {
                                echo "ERROR: Failed to send email for 'Run Tests' stage: ${emailErr}"
                                // This error here means the emailext step itself failed,
                                // which will be visible in the job's console log.
                            }
                            echo "DEBUG: Exiting 'Run Tests' finally block."
                        }
                    }
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                echo "DEBUG: Starting 'Generate Coverage Report' stage."
                sh 'export PATH=/opt/homebrew/bin:$PATH && npm run coverage || true'
                echo "DEBUG: 'Generate Coverage Report' stage completed."
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    def auditStatus = true
                    try {
                        echo "DEBUG: Starting 'NPM Audit (Security Scan)' stage."
                        sh 'export PATH=/opt/homebrew/bin:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin && npm audit'
                        echo "DEBUG: NPM audit command completed successfully."
                    } catch (err) {
                        auditStatus = false
                        currentBuild.result = 'FAILURE'
                        echo "ERROR: NPM audit command failed with error: ${err}"
                    } finally {
                        echo "DEBUG: Entering 'NPM Audit' finally block. auditStatus=${auditStatus}"
                        try {
                            emailext(
                                to: 'riteshagarwal.au@gmail.com',
                                subject: "Jenkins Security Scan Stage - ${auditStatus ? 'SUCCESS' : 'FAILURE'} - ${env.JOB_NAME} #${env.BUILD_NUMBER}", // Added job and build info
                                body: """<p>The 'Security Scan' stage for job <b>${env.JOB_NAME}</b> build <b>#${env.BUILD_NUMBER}</b> has ${auditStatus ? 'succeeded' : 'failed'}.</p>
                                         <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                                         <p>Check Jenkins logs for details.</p>""",
                                mimeType: 'text/html',
                                attachLog: true
                            )
                            echo "DEBUG: Email for 'NPM Audit' stage sent successfully from pipeline."
                        } catch (emailErr) {
                            echo "ERROR: Failed to send email for 'NPM Audit' stage: ${emailErr}"
                        }
                        echo "DEBUG: Exiting 'NPM Audit' finally block."
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "DEBUG: Starting 'SonarQube Analysis' stage."
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh 'export PATH=/opt/homebrew/bin:$PATH && sonar-scanner -Dsonar.login=$SONAR_TOKEN'
                }
                echo "DEBUG: 'SonarQube Analysis' stage completed."
            }
        }
    }
    // You might also want a global email notification at the end of the pipeline
    post {
        always {
            echo "DEBUG: Entering global 'post-build' section. Sending final email..."
            // This is another option for a single email for the whole build result
            // You can use currentBuild.result here
            emailext (
                to: 'riteshagarwal.au@gmail.com',
                subject: "Jenkins Pipeline ${currentBuild.fullDisplayName} Finished: ${currentBuild.result}",
                body: "Pipeline ${currentBuild.fullDisplayName} finished with status: ${currentBuild.result}.<br/>Build URL: ${env.BUILD_URL}",
                mimeType: 'text/html'
            )
            echo "DEBUG: Global email sent."
        }
    }
}