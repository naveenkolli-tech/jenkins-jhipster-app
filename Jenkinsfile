pipeline {
    agent any

    tools {
        jdk     'JDK17'
        nodejs  'Node24'
        maven   'Maven3'
    }

    environment {
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
        JAVA_HOME = "${tool 'JDK17'}"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Tools') {
            steps {
                sh '''
                    echo "Java version:"
                    java -version

                    echo "Maven version:"
                    mvn -version

                    echo "Node version:"
                    node --version

                    echo "NPM version:"
                    npm --version
                '''
            }
        }

        stage('Install frontend deps') {
            steps {
                sh '''
                    rm -rf node_modules package-lock.json
                    npm install --legacy-peer-deps
                '''
            }
        }

        stage('Build backend') {
            steps {
                sh '''
                    mvn clean verify -DskipTests
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    mvn test
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Build failed. Check logs above."
        }
        success {
            echo "✅ Build completed successfully!"
        }
    }
}
