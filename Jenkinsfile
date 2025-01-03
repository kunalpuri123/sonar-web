pipeline {
    agent any
    tools {
        nodejs 'nodejs-22.9.0' // Ensure this matches your Node.js version in Jenkins
    }

    environment {
        // Set the correct paths for macOS or Docker containers
        NODEJS_HOME = '/Users/kunalpuri/.nvm/versions/node/v22.9.0/bin'  // Path to Node.js
        SONAR_SCANNER_PATH = '/opt/homebrew/bin/sonar-scanner'  // Path to sonar-scanner
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Set the PATH and install dependencies using npm
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                // Run linting to ensure code quality
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm run build
                '''
            }
        }

        stage('Wait for SonarQube') {
            steps {
                script {
                    // Ensure SonarQube is up and running before starting analysis
                    sh 'sleep 30'  // Adjust sleep time as needed
                    // Optionally check SonarQube status
                    sh 'curl -u admin:admin http://localhost:9000/api/server/version'
                }
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Accessing the SonarQube token stored in Jenkins credentials
            }
            steps {
                // Ensure that sonar-scanner is in the PATH
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                which sonar-scanner || echo "SonarQube scanner not found. Please install it."
                sonar-scanner -Dsonar.projectKey=register-form \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.token=sqp_07ac1bfa9ad97d86caf9ce27980ebe657b0b2b30

                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
