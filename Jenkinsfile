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
        nodejs 'Node 24'
        maven 'Maven 3'
        jdk 'JDK 21'
    }

    environment {
        NPM_CONFIG_AUDIT = 'false'
        NPM_CONFIG_FUND  = 'false'
        CI = 'true'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Environment') {
            steps {
                script {
                    def NODE_HOME = tool name: 'Node 24',
                        type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'

                    withEnv(["PATH+NODE=${NODE_HOME}/bin"]) {
                        sh '''
                            echo "=== Environment Information ==="
                            echo "JAVA_HOME: $JAVA_HOME"
                            java -version
                            mvn --version
                            node --version
                        '''
                    }
                }
            }
        }

        stage('Update pom.xml for JDK 21') {
            steps {
                sh '''
                    sed -i "s/<java.version>.*<\\/java.version>/<java.version>21<\\/java.version>/" pom.xml || true
                    sed -i "s/<release>.*<\\/release>/<release>21<\\/release>/" pom.xml || true
                    sed -i "s/<maven.compiler.release>.*<\\/maven.compiler.release>/<maven.compiler.release>21<\\/maven.compiler.release>/" pom.xml || true
                    grep "<java.version>" pom.xml || true
                '''
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                script {
                    def NODE_HOME = tool name: 'Node 24',
                        type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'

                    withEnv(["PATH+NODE=${NODE_HOME}/bin"]) {
                        timeout(time: 20, unit: 'MINUTES') {
                            sh '''
                                rm -rf node_modules package-lock.json
                                npm install --legacy-peer-deps --no-progress --loglevel=info
                            '''
                        }
                    }
                }
            }
        }

        stage('Build Backend') {
            steps {
                sh 'mvn clean compile -DskipTests'
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    def NODE_HOME = tool name: 'Node 24',
                        type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'

                    withEnv(["PATH+NODE=${NODE_HOME}/bin"]) {
                        timeout(time: 15, unit: 'MINUTES') {
                            sh '''
                                if [ -f "package.json" ] && grep -q "build" package.json; then
                                    npm run build
                                else
                                    echo "No frontend build required"
                                fi
                            '''
                        }
                    }
                }
            }
        }

        stage('Package Application') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar, target/*.war',
                             fingerprint: true,
                             allowEmptyArchive: true
        }
        always {
            echo "Build status: ${currentBuild.currentResult}"
            cleanWs()
        }
    }
}
