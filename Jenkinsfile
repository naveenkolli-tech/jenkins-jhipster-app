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
=======
                echo 'Installing frontend dependencies'
                sh '''
                    node --version
                    npm --version
                    npm install --legacy-peer-deps --no-audit --no-fund
                '''
>>>>>>> 3db812c3 (Enhance Jenkinsfile with logging and npm command)
            }
        }

        stage('Build backend') {
            steps {
                echo 'Building backend'
                sh '''
                    mvn clean verify -DskipTests
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
                sh '''
                    mvn test
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}

