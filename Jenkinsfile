pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    sh 'mvn clean test'

                    recordCoverage(tools: [[parser: 'JACOCO']])
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn package'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
