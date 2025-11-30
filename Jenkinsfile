pipeline {
    agent any

    tools {
        nodejs 'node18'
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Prepare JRE') {
            steps {
                sh '''
                  set -euxo pipefail
                  # Install Java 17 JRE using the available package manager
                  if command -v java >/dev/null 2>&1; then
                    echo "Java already present: $(java -version 2>&1 | head -n1)"
                  elif command -v apt-get >/dev/null 2>&1; then
                    apt-get update
                    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends openjdk-17-jre-headless
                  elif command -v yum >/dev/null 2>&1; then
                    yum install -y java-17-openjdk-headless
                  elif command -v dnf >/dev/null 2>&1; then
                    dnf install -y java-17-openjdk-headless
                  elif command -v zypper >/dev/null 2>&1; then
                    zypper --non-interactive install -y java-17-openjdk
                  elif command -v apk >/dev/null 2>&1; then
                    apk add --no-cache openjdk17-jre-headless
                  else
                    echo "No supported package manager found to install Java 17" >&2
                    exit 1
                  fi

                  # Show resolved java path
                  command -v java
                  java -version
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                // Disable auto-provisioning and point to the local Java
                SONAR_SCANNER_SKIP_JRE_PROVISIONING = 'true'
                SONAR_SCANNER_JAVA_EXE_PATH = '/usr/bin/java'
            }
            steps {
                // Resolve exact java path (handles distros where java isn't /usr/bin/java)
                script {
                    def javaPath = sh(script: "command -v java", returnStdout: true).trim()
                    env.SONAR_SCANNER_JAVA_EXE_PATH = javaPath
                }

                withSonarQubeEnv('SonarQube') {
                    sh 'npx @sonar/scan'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
    }
}
