pipeline {
  agent any

  tools {
    jdk 'JDK17'       // name must match Manage Jenkins > Global Tool Configuration
    nodejs 'node18'
  }

  stages {
    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }

    stage('SonarQube Analysis') {
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
