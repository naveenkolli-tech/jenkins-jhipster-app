// 🔒 Keep ALL build history, keep artifacts ONLY for last 2 builds
properties([
  buildDiscarder(
    logRotator(
      numToKeepStr: '-1',
      artifactNumToKeepStr: '2'
    )
  )
])

pipeline {
    agent any

    tools {
        nodejs 'Node 24'     // Jenkins Global Tool Configuration
        maven 'Maven 3'
        jdk 'JDK 21'
    }

    environment {
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

                    echo "JAVA_HOME:"
                    echo "$JAVA_HOME"
                    java -version

                    echo ""
                    echo "Maven version:"
                    mvn --version

                    echo ""
                    echo "Node version:"
                    node --version

                    node_major=$(node -v | cut -d. -f1 | tr -d 'v')

                    if [ "$node_major" -lt 24 ]; then
                      echo "⚠️ WARNING: Node 24 is recommended"
                      echo "⚠️ Current Node version: $(node -v)"
                    else
                      echo "✅ Node version is compliant"
                    fi
                '''
            }
        }

        stage('Update pom.xml for JDK 21') {
            steps {
                sh '''
                    sed -i "s/<java.version>.*<\\/java.version>/<java.version>21<\\/java.version>/" pom.xml || true
                    sed -i "s/<release>.*<\\/release>/<release>21<\\/release>/" pom.xml || true
                    sed -i "s/<maven.compiler.release>.*<\\/maven.compiler.release>/<maven.compiler.release>21<\\/maven.compiler.release>/" pom.xml || true

                    echo "Updated Java version:"
                    grep "<java.version>" pom.xml || true
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
                    if [ -f "package.json" ] && grep -q "build" package.json; then
                        npm run build || true
                    else
                        echo "No frontend build required"
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
            cleanWs()   // 🔥 Prevents workspace disk growth permanently
        }

        success {
            archiveArtifacts artifacts: 'target/*.jar, target/*.war', fingerprint: true
        }
    }
}
