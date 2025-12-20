pipeline {
    agent any

    tools {
        nodejs 'Node 24'
        maven 'Maven 3'
        jdk 'JDK 17'

    }

    environment {
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
                sh '''

                    node --version
                    npm --version

                    echo "Node version:"
                    node --version

                    echo "NPM version:"
                    npm --version

 
                    rm -rf node_modules
                    npm install --legacy-peer-deps
                '''
            }
        }

        stage('Build backend') {
            steps {
                sh 'mvn clean verify -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}

