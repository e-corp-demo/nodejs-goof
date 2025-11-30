pipeline {
  agent any
  tools {
    jdk 'JDK17'     // must match Manage Jenkins > Global Tool Configuration
    nodejs 'node25'
  }
  stages {
    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }

    stage('SonarQube Analysis') {
      environment {
        SONAR_SCANNER_SKIP_JRE_PROVISIONING = 'true'
      }
      steps {
        script {
          // tools{} adds JDK17/bin to PATH, so this finds the right java
          env.SONAR_SCANNER_JAVA_EXE_PATH = sh(
            script: 'command -v java',
            returnStdout: true
          ).trim()
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
