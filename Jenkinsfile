pipeline {
    agent any

    // Define the SonarQube Scanner tool configured in Jenkins
    tools {
        nodejs 'node18'
    }

    stages {

        stage('Install Dependencies') {
            steps {
                // Install project dependencies
                sh 'npm install'
                // sh 'npm install -D @sonar/scan'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                // Force Alpine-compatible JRE provisioning for the scanner engine
                SONAR_SCANNER_OS   = 'alpine'
                // Explicit arch (optional, but set for arm64 runners)
                SONAR_SCANNER_ARCH = 'aarch64'
            }
            steps {
                // The 'SonarQube' name must match your configured server in Manage Jenkins -> System
                withSonarQubeEnv('SonarQube') {
                    // Run the SonarScanner via the NPM bootstrapper
                    sh 'npx @sonar/scan'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Wait for SonarQube analysis to complete and check the quality gate status
                timeout(time: 1, unit: 'MINUTES') {
                    // If webhooks are configured, this waits efficiently; otherwise it polls
                    waitForQualityGate abortPipeline: false
                }
            }
        }
    }
}
