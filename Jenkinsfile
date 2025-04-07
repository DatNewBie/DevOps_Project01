pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    sh 'mvn clean verify'

                    junit '**/target/surefire-reports/TEST-*.xml'

                    recordCoverage(
                        aggregating: true,
                        glob: '**/target/site/jacoco/jacoco.xml'
                    )    

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
