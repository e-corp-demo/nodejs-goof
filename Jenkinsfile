pipeline {
    agent any

    tools {
        nodejs 'node18'
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
                // sh 'npm install -D @sonar/scan'
            }
        }

        stage('Prepare JRE (Alpine)') {
            when { expression { true } } // keep or gate by label if needed
            steps {
                // Install a local Java 17 JRE on Alpine
                sh 'apk add --no-cache openjdk17-jre-headless'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                // Disable JRE auto-provisioning and point to the local JRE
                SONAR_SCANNER_SKIP_JRE_PROVISIONING = 'true'
                SONAR_SCANNER_JAVA_EXE_PATH = '/usr/lib/jvm/java-17-openjdk/bin/java'
            }
            steps {
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
