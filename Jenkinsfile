pipeline {
    agent any

    tools {
    nodejs 'Node24'
    maven 'Maven3'
}
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install frontend deps') {
            steps {
                sh 'npm install'
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
