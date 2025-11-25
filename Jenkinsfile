pipeline {
  agent any
  tools { maven 'Maven-3.8.8'; jdk 'OpenJDK-17' }

  stages {
    stage('Build') {
      steps { script { isUnix() ? sh('mvn -B clean package') : bat('mvn -B clean package') } }
    }

    stage('Test') {
      steps { junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true }
    }

    stage('Archive') {
      steps { archiveArtifacts artifacts: 'target/*.jar' }
    }

    stage('Run') {
      steps {
        script {
          if (isUnix()) {
            sh 'java -jar $(ls target/*.jar | head -n1) || true'
          } else {
            bat 'for %%f in (target\\*.jar) do set JAR=%%f & java -jar "%JAR%" || exit /b 0'
          }
        }
      }
    }
  }
}
