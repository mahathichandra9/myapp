pipeline {
  agent any
  tools { maven 'Maven-3.8.8'; jdk 'OpenJDK-17' }

  stages {
    stage('Build') {
      steps {
        script {
          isUnix() ? sh('mvn -B clean package') : bat('mvn -B clean package')
        }
      }
    }

    stage('Test') {
      steps { junit '**/target/surefire-reports/*.xml', allowEmptyResults: true }
    }

    stage('Archive') {
      steps { archiveArtifacts 'target/*.jar' }
    }

    stage('Run') {
      steps {
        script {
          isUnix()
            ? sh('java -jar $(ls target/*.jar | head -n1) || true')
            : bat('for %%f in (target\\*.jar) do (java -jar "%%f" || exit /b 0)')
        }
      }
    }
  }
}
