pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    sh 'mvn clean verify'

                    junit '**/test-results.xml' 

                    publishCoverage adapters: [jacocoAdapter('**/target/site/jacoco/jacoco.xml')]
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
