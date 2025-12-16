pipeline {
    agent any

    tools {
        nodejs 'Node 24'
        maven 'Maven 3'
    }

    environment {
        // Prevent npm permission & audit noise
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install frontend deps') {
            steps {
                echo 'Installing frontend dependencies'
                sh '''
                    echo "Node version:"
                    node --version

                    echo "NPM version:"
                    npm --version

                    echo "Cleaning old node_modules"
                    rm -rf node_modules

                    echo "Installing dependencies"
                    npm install --legacy-peer-deps
                '''
            }
        }

        stage('Build backend') {
            steps {
                echo 'Building backend (skip tests)'
                sh '''
                    mvn clean verify -DskipTests
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running backend tests'
                sh '''
                    mvn test
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
        always {
            echo '🧹 Pipeline finished'
        }
    }
}
