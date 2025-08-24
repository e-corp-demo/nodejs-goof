pipeline {
    agent any

    // Define the SonarQube Scanner tool configured in Jenkins
    tools {
        // 'SonarScanner' is the name you gave the tool in Global Tool Configuration
        nodejs 'node18' // Assuming you have a NodeJS tool configured
    }

    stages {

        stage('Install Dependencies') {
            steps {
                // Install project dependencies
                sh 'npm install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // The 'SonarQube' part should match the name of your SonarQube server
                // configured in Manage Jenkins -> System.
                withSonarQubeEnv('SonarQube') {
                    // Run the SonarScanner
                    sh 'sonar-scanner'
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
