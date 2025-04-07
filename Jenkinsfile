pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    sh 'mvn clean verify'

                    junit '**/target/surefire-reports/TEST-*.xml'

                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java',
                        inclusionPattern: '**/*',
                        exclusionPattern: '**/test/**'
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
