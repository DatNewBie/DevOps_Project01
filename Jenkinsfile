pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    sh 'mvn clean install'
                    
                    sh 'mvn clean test jacoco:report'

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
