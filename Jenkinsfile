pipeline {
    agent any

    tools {
<<<<<<< HEAD
        nodejs 'Node 24'
        maven 'Maven 3'
    }

    environment {
        // Prevent npm permission & audit noise
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
    }

    stages {

=======
        maven 'Maven3'
        nodejs 'Node18'
    }

    stages {
>>>>>>> 0cab569a (Add Jenkins pipeline)
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

<<<<<<< HEAD
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
=======
        stage('Install frontend dependencies') {
            steps {
                sh 'npm install'
>>>>>>> 0cab569a (Add Jenkins pipeline)
            }
        }

        stage('Build backend') {
            steps {
<<<<<<< HEAD
                echo 'Building backend (skip tests)'
                sh '''
                    mvn clean verify -DskipTests
                '''
=======
                sh 'mvn clean package -DskipTests'
>>>>>>> 0cab569a (Add Jenkins pipeline)
            }
        }

        stage('Test') {
            steps {
<<<<<<< HEAD
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
=======
                sh 'mvn test'
            }
        }
    }
}

>>>>>>> 0cab569a (Add Jenkins pipeline)
