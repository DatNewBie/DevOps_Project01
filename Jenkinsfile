pipeline {
    agent any

    environment {
        // Danh sách thư mục bị thay đổi có chứa pom.xml (service)
        CHANGED_SERVICES = ''
    }

    stages {
        stage('Detect Changed Services') {
            steps {
                script {
                    // So sánh với nhánh main để phát hiện file bị thay đổi
                    def changedFiles = sh(
                        script: "git fetch origin main && git diff --name-only origin/main...HEAD",
                        returnStdout: true
                    ).trim().split('\n')

                    // Tìm các thư mục cha cấp 1 của file thay đổi
                    def serviceDirs = [] as Set
                    for (file in changedFiles) {
                        def dir = file.tokenize('/')[0]
                        if (fileExists("${dir}/pom.xml")) {
                            serviceDirs << dir
                        }
                    }

                    CHANGED_SERVICES = serviceDirs.join(',')
                    echo "📁 Các service bị thay đổi: ${CHANGED_SERVICES}"
                }
            }
        }

        stage('Test') {
            when {
                expression { return env.CHANGED_SERVICES?.trim() }
            }
            steps {
                script {
                    CHANGED_SERVICES.split(',').each { service ->
                        echo "🧪 Testing ${service}..."
                        dir("${service}") {
                            sh 'mvn clean verify'
                            recordCoverage tools: [[parser: 'JACOCO']]
                        }
                    }
                }
            }
        }

        stage('Build') {
            when {
                expression { return env.CHANGED_SERVICES?.trim() }
            }
            steps {
                script {
                    CHANGED_SERVICES.split(',').each { service ->
                        echo "🔧 Building ${service}..."
                        dir("${service}") {
                            sh 'mvn package -DskipTests'
                            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                        }
                    }
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

