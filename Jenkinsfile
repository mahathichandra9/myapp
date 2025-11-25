pipeline {
  agent any

  tools {
    // Keep these names in sync with Manage Jenkins -> Global Tool Configuration
    maven 'Maven-3.8.8'
    jdk 'OpenJDK-17'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
        echo "Checked out commit: ${env.GIT_COMMIT}"
      }
    }

    stage('Build') {
      steps {
        script {
          if (isUnix()) {
            // Unix / Linux / macOS agents
            sh 'mvn -B clean package'
          } else {
            // Windows agents
            // Maven is put on PATH by the "tools" block, so just call mvn
            bat 'mvn -B clean package'
          }
        }
      }
    }

    stage('Test Results') {
      steps {
        junit '**/target/surefire-reports/*.xml'
      }
    }

    stage('Archive Artifacts') {
      steps {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }

    stage('Run App (Smoke Test)') {
      steps {
        script {
          if (isUnix()) {
            sh '''
              JAR=$(ls target/*.jar | head -n1)
              if [ -z "$JAR" ]; then echo "No jar found"; exit 0; fi
              echo "Found: $JAR"
              timeout 8 java -jar "$JAR" || true
            '''
          } else {
            // Windows: find jar and run it (keeps console output visible)
            bat '''
              for %%f in (target\\*.jar) do set JAR=%%f
              if "%JAR%"=="" (
                echo No jar found
              ) else (
                echo Found: %JAR%
                java -jar "%JAR%" || exit /b 0
              )
            '''
          }
        }
      }
    }
  }

  post {
    success { echo "Pipeline completed successfully!" }
    failure { echo "Pipeline failed." }
    always { echo "Result: ${currentBuild.currentResult}" }
  }
}
