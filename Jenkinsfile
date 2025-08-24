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
                sh 'npm install -D @sonar/scan'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // The 'SonarQube' part should match the name of your SonarQube server
                // configured in Manage Jenkins -> System.
                withSonarQubeEnv('SonarQube') {
                    // Run the SonarScanner
                    // sh 'sonar-scanner'
                    sh 'npx @sonar/scan'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                // Wait for SonarQube analysis to complete and check the quality gate status
                timeout(time: 1, unit: 'HOURS') {
                    // The 'webhook' option is more efficient if you've configured webhooks in SonarQube.
                    // Otherwise, it will poll for the result.
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
