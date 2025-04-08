pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    // Chạy Maven command để test và tạo báo cáo coverage
                    sh 'mvn test'

                    recordCoverage(tools: [[parser: 'JACOCO']])

                    // Đảm bảo JUnit results
                    junit '**/target/test-classes/testng*.xml'  // Đảm bảo file junit nằm ở đúng đường dẫn
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
