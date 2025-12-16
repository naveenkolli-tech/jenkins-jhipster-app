pipeline {
    agent any

    tools {
        // Remove Node.js from tools block since it's not installed in Jenkins
        // Use the Maven frontend plugin to install Node.js
        maven 'Maven 3'
        jdk 'JDK 17'
    }

    environment {
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
        // Explicitly set JAVA_HOME to ensure correct JDK
        JAVA_HOME = "${tool 'JDK 17'}"
        // Force Maven to use the correct Java version
        MAVEN_OPTS = "-Djava.version=17"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Java Version') {
            steps {
                sh '''
                    echo "=== Java Version ==="
                    echo "JAVA_HOME: ${JAVA_HOME}"
                    "${JAVA_HOME}/bin/java" -version
                    echo ""
                    echo "=== Maven Version ==="
                    mvn --version
                '''
            }
        }

        stage('Install frontend deps') {
            steps {
                sh '''
                    echo "=== Node.js Version (from Maven frontend plugin will install correct version) ==="
                    # Clean node_modules
                    rm -rf node_modules package-lock.json
                    
                    # Let Maven handle Node.js installation via frontend-maven-plugin
                    # This ensures the correct Node.js version (24.11.1) is installed
                    mvn clean compile -DskipTests -DskipNodeModules=false
                '''
            }
        }

        stage('Build backend') {
            steps {
                sh '''
                    echo "=== Building backend with JDK 17 ==="
                    # Use the JAVA_HOME explicitly
                    "${JAVA_HOME}/bin/java" -version
                    
                    # Build with skipTests for faster compilation
                    mvn clean compile -DskipTests
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "=== Running tests ==="
                    mvn test
                '''
            }
        }

        stage('Package') {
            steps {
                sh '''
                    echo "=== Packaging application ==="
                    mvn package -DskipTests
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
