pipeline {
    agent any

    tools {
        // Use the SAME tools as deploy pipeline
        nodejs 'Node 24'
        maven 'Maven 3'
        jdk 'JDK 17'
    }

    environment {
        // Match deploy pipeline environment
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                    echo "=== Environment Information ==="
                    echo "JAVA_HOME: ${JAVA_HOME}"
                    echo "Java version:"
                    java -version
                    echo ""
                    echo "Maven version:"
                    mvn --version
                    echo ""
                    echo "Node version:"
                    node --version
                '''
            }
        }

        stage('Update pom.xml for JDK 1') {
            steps {
                sh '''
                    # Update pom.xml to use Java 21
                    sed -i "s/<java.version>.*<\\/java.version>/<java.version>21<\\/java.version>/" pom.xml || true
                    
                    # Update compiler release if exists
                    sed -i "s/<release>.*<\\/release>/<release>21<\\/release>/" pom.xml 2>/dev/null || true
                    sed -i "s/<maven.compiler.release>.*<\\/maven.compiler.release>/<maven.compiler.release>21<\\/maven.compiler.release>/" pom.xml 2>/dev/null || true
                    
                    echo "Updated pom.xml java.version:"
                    grep "<java.version>" pom.xml
                '''
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                sh '''
                    rm -rf node_modules package-lock.json
                    npm install --legacy-peer-deps
                '''
            }
        }

        stage('Build Backend') {
            steps {
                sh 'mvn clean compile -DskipTests'
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                    # Check if frontend needs to be built
                    if [ -f "package.json" ] && grep -q "build" package.json; then
                        npm run build || echo "Frontend build script not found, continuing..."
                    fi
                '''
            }
        }

        stage('Package Application') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            echo "Build status: ${currentBuild.currentResult}"
        }
        success {
            archiveArtifacts artifacts: 'target/*.jar, target/*.war', fingerprint: true
        }
    }
}
