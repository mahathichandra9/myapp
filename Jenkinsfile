pipeline {
    agent any

    tools {
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
                sh 'mvn -B clean package'
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
                sh '''
                    JAR=$(ls target/*.jar | head -n1)
                    echo "Running: $JAR"
                    timeout 8 java -jar "$JAR" || true
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
