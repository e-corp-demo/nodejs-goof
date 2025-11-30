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

        stage('SonarQube Analysis') {
            steps {
                // Matches the name configured in Manage Jenkins -> System
                withSonarQubeEnv('SonarQube') {
                    // No OS/arch overrides; let the scanner auto-provision a glibc JRE
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
