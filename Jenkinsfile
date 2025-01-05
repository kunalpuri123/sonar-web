pipeline {
    agent any
    tools {
        nodejs 'nodejs-22.9.0' // Ensure this matches your Node.js version in Jenkins
        sonarScanner 'sonarqube-scanner'
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



        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Accessing the SonarQube token stored in Jenkins credentials
            }
            steps {
                // Ensure that sonar-scanner is in the PATH
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                which sonar-scanner || echo "SonarQube scanner not found. Please install it."
                sonar-scanner -Dsonar.projectKey=pipe2 \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.token=sqp_3e0e34585435fe94f3269900a07b25402d5c6884

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
