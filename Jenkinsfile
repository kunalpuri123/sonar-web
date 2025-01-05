pipeline {
    agent any
    tools {
        nodejs 'nodejs-22.9.0'  // Ensure this matches your Node.js version in Jenkins
      
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token') // Accessing the SonarQube token stored in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                npm install
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Make sure SonarQube Scanner is available in the environment
                    def scannerHome = tool name: 'SonarQubeScanner', type: 'SonarQubeScannerInstallation'
                    sh """
                    ${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=pipe2 \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.token=${SONAR_TOKEN}
                    """
                }
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
    }
}
