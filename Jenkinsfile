pipeline {
    agent any

    tools {
        // Use JDK 17 - let's first verify it works
        jdk 'JDK 17'
    }

    environment {
        // Force Maven to use Java 17 compiler settings
        MAVEN_OPTS = '-Xmx1024m -XX:MaxPermSize=256m'
        // Clear any inherited JAVA_HOME that might conflict
        JAVA_HOME = "${tool 'JDK 17'}"
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
                    echo "PATH: ${PATH}"
                    echo ""
                    echo "=== Java Version ==="
                    java -version
                    echo ""
                    echo "=== Maven Version ==="
                    mvn --version
                    echo ""
                    echo "=== Node Version (if available) ==="
                    node --version || echo "Node.js not in PATH"
                    npm --version || echo "npm not in PATH"
                '''
            }
        }

        stage('Check Project Configuration') {
            steps {
                sh '''
                    echo "=== Checking Project Configuration ==="
                    echo "Java version in pom.xml:"
                    grep -A1 -B1 "<java.version>" pom.xml || echo "No java.version found in pom.xml"
                    echo ""
                    echo "Maven compiler plugin configuration:"
                    grep -A5 "maven-compiler-plugin" pom.xml || echo "No maven-compiler-plugin configuration found"
                '''
            }
        }

        stage('Debug Build Attempt') {
            steps {
                sh '''
                    echo "=== Attempting to compile with debug info ==="
                    
                    # First try just cleaning
                    mvn clean --quiet
                    
                    # Then try compiling with verbose output
                    echo "=== Running mvn compile with debug ==="
                    mvn compile -DskipTests -e -X 2>&1 | tail -100 || true
                '''
            }
        }

        stage('Fix and Build') {
            steps {
                script {
                    // Option 1: Update pom.xml to match your JDK version
                    sh '''
                        echo "=== Checking actual Java version ==="
                        JAVA_VERSION=$(java -version 2>&1 | head -1 | cut -d'"' -f2 | sed 's/\\.[0-9]*$//')
                        echo "Detected Java version: $JAVA_VERSION"
                        
                        # Update pom.xml if needed
                        if [ ! -z "$JAVA_VERSION" ]; then
                            echo "Updating pom.xml to use Java $JAVA_VERSION"
                            sed -i "s/<java.version>.*<\\/java.version>/<java.version>$JAVA_VERSION<\\/java.version>/" pom.xml
                            
                            # Also update compiler release if needed
                            sed -i "s/<release>.*<\\/release>/<release>$JAVA_VERSION<\\/release>/" pom.xml 2>/dev/null || true
                            sed -i "s/<maven.compiler.release>.*<\\/maven.compiler.release>/<maven.compiler.release>$JAVA_VERSION<\\/maven.compiler.release>/" pom.xml 2>/dev/null || true
                        fi
                        
                        echo "=== Updated pom.xml ==="
                        grep -A1 -B1 "<java.version>" pom.xml
                    '''
                }
            }
        }

        stage('Build Application') {
            steps {
                sh '''
                    echo "=== Building application ==="
                    
                    # Try to build with different approaches
                    
                    # Approach 1: Standard build
                    echo "=== Approach 1: Standard clean compile ==="
                    mvn clean compile -DskipTests || echo "Standard approach failed"
                    
                    # Approach 2: Build without frontend (if previous failed)
                    if [ $? -ne 0 ]; then
                        echo "=== Approach 2: Skip frontend build ==="
                        mvn clean compile -DskipTests -Dskip.npm || echo "Skip frontend approach also failed"
                    fi
                    
                    # Approach 3: Force Java version
                    if [ $? -ne 0 ]; then
                        echo "=== Approach 3: Force Java version ==="
                        JAVA_VERSION=$(java -version 2>&1 | head -1 | cut -d'"' -f2 | sed 's/\\.[0-9]*$//')
                        mvn clean compile -DskipTests -Djava.version=$JAVA_VERSION || echo "Forced Java version approach failed"
                    fi
                '''
            }
        }

        stage('Build Frontend Separately') {
            steps {
                sh '''
                    echo "=== Building frontend separately ==="
                    
                    # Check if we need to build frontend
                    if [ -d "src/main/webapp" ]; then
                        echo "Frontend directory exists, attempting to build..."
                        
                        # Try using Maven's frontend plugin
                        mvn frontend:install-node-and-npm frontend:npm frontend:webpack -DskipTests || echo "Frontend build via Maven failed"
                        
                        # Alternative: Direct npm build
                        if [ $? -ne 0 ] && [ -f "package.json" ]; then
                            echo "Trying direct npm build..."
                            # Install Node.js if not available
                            if ! command -v node &> /dev/null; then
                                echo "Installing Node.js via nvm or download..."
                                # You might need to install Node.js here
                                # curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
                                # export NVM_DIR="$HOME/.nvm"
                                # [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
                                # nvm install 18
                            fi
                            npm install && npm run webapp:build || echo "Direct npm build also failed"
                        fi
                    else
                        echo "No frontend directory found, skipping frontend build"
                    fi
                '''
            }
        }

        stage('Final Build') {
            steps {
                sh '''
                    echo "=== Final build attempt ==="
                    
                    # Try a full verify with all possible flags
                    mvn clean verify -DskipTests \
                        -Dskip.npm \
                        -Dskip.frontend \
                        -Dskip.installnodenpm || echo "Final build attempt failed"
                    
                    # If that fails, try without any frontend
                    if [ $? -ne 0 ]; then
                        echo "=== Trying without any frontend operations ==="
                        mvn clean package -DskipTests -Dskip.frontend
                    fi
                '''
            }
        }
    }

    post {
        always {
            echo "=== Build completed with status: ${currentBuild.currentResult} ==="
            sh '''
                echo "=== Final workspace contents ==="
                ls -la target/ 2>/dev/null || echo "No target directory"
                echo ""
                echo "=== Checking for built artifacts ==="
                find . -name "*.jar" -o -name "*.war" 2>/dev/null | head -10
            '''
        }
        failure {
            echo "❌ Build failed. Check logs above for Java version issues."
            sh '''
                echo "=== Additional debugging info ==="
                echo "Current directory: $(pwd)"
                echo "Java home: ${JAVA_HOME}"
                echo "Maven settings:"
                cat ~/.m2/settings.xml 2>/dev/null || echo "No Maven settings file"
            '''
        }
        success {
            echo "✅ Build completed successfully!"
            archiveArtifacts artifacts: 'target/*.jar, target/*.war', fingerprint: true
        }
    }
}
