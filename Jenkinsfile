pipeline {
    agent any

    tools {
        // Only use JDK 17 - let Maven handle Node.js via frontend-maven-plugin
        jdk 'JDK 17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Java Version') {
            steps {
                script {
                    // Get the JDK 17 path from Jenkins tools
                    def javaHome = tool name: 'JDK 17', type: 'jdk'
                    // Set JAVA_HOME explicitly
                    env.JAVA_HOME = javaHome
                    // Add to PATH
                    env.PATH = "${javaHome}/bin:${env.PATH}"
                }
                sh '''
                    echo "=== Java Version Information ==="
                    echo "JAVA_HOME: ${JAVA_HOME}"
                    java -version
                    which java
                    echo ""
                    echo "=== Maven Information ==="
                    mvn --version
                '''
            }
        }

        stage('Build Full Application') {
            steps {
                sh '''
                    echo "=== Building JHipster application ==="
                    echo "Using Java from: ${JAVA_HOME}"
                    
                    # Clean and build with Maven
                    # Maven will handle Node.js installation via frontend-maven-plugin
                    mvn clean compile -DskipTests
                '''
            }
        }

        stage('Run Tests') {
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
