pipeline {
    agent any

    environment {
        PROJECT_NAME = "MyApp"
    }

    stages {

        stage('1. Checkout Code') {
            steps {
                // 📝 Fetch latest code from GitHub
                git url: 'https://github.com/ritssoft/sit75381c.git', branch: 'main'
            }
        }

        stage('2. Code Linting') {
            steps {
                // 📝 Check for syntax/style issues using a linter
                echo 'Running ESLint or Pylint to check code style...'
                // Example: sh 'eslint . || true' or sh 'pylint src/'
            }
        }

        stage('3. Build') {
            steps {
                // 📝 Compile the application
                echo 'Building the application...'
                // Example for Java: sh 'mvn clean package'
                // Example for Node.js: sh 'npm run build'
            }
        }

        stage('4. Unit Test') {
            steps {
                // 📝 Run unit tests to validate code logic
                echo 'Running unit tests...'
                // Example: sh 'pytest tests/' or sh 'npm test'
            }
        }

        stage('5. Code Coverage') {
            steps {
                // 📝 Measure how much of your code is tested
                echo 'Generating code coverage report...'
                // Example: sh 'coverage run -m pytest && coverage report'
            }
        }

        stage('6. Security Scan') {
            steps {
                // 📝 Scan dependencies or code for vulnerabilities
                echo 'Running security scan...'
                // Example: sh 'npm audit' or sh 'trivy fs .'
            }
        }

        stage('7. Deploy to Dev Environment') {
            steps {
                // 📝 Deploy build artifacts to a dev/test environment
                echo 'Deploying to development environment...'
                // Example: sh './deploy.sh dev'
            }
        }
    }
}