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
      environment {
        SONAR_SCANNER_SKIP_JRE_PROVISIONING = 'true'
        SONAR_SCANNER_JAVA_EXE_PATH = "${env.JAVA_HOME}/bin/java"
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
