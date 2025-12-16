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
        JAVA_HOME = "${tool 'JDK 17'}"
        MAVEN_HOME = "${tool 'Maven 3'}"
        NODEJS_HOME = "${tool 'Node 24'}"
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${NODEJS_HOME}/bin:${env.PATH}"
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
                    mvn --version

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

        stage('Build & Test') {
            steps {
                sh '''
                    mvn clean verify
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
