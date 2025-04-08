pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    // Chạy Maven command để test và tạo báo cáo coverage
                    sh 'mvn clean test jacoco:report'

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
