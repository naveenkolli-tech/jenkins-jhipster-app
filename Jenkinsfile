pipeline {
    agent any

    tools {
        nodejs 'Node 24'
        maven 'Maven 3'
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
                    node --version
                    npm --version
                    npm install --legacy-peer-deps --no-audit --no-fund
                '''
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
