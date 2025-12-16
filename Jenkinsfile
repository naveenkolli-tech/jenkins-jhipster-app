pipeline {
    agent any

    tools {
        nodejs 'Node 24'
        maven 'Maven 3'
<<<<<<< HEAD
        jdk 'JDK 17'
    }

    environment {
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
=======
>>>>>>> 1cfe3cf0 (Modify Jenkinsfile to install npm with legacy flags)
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install frontend deps') {
            steps {
<<<<<<< HEAD
                sh '''
                    echo "Node version:"
                    node --version

                    echo "NPM version:"
                    npm --version

                    rm -rf node_modules
                    npm install --legacy-peer-deps
                '''
=======
                sh 'npm install --legacy-peer-deps'
>>>>>>> 1cfe3cf0 (Modify Jenkinsfile to install npm with legacy flags)
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

