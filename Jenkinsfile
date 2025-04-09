pipeline {
    agent any

    environment {
        // Danh sách thư mục bị thay đổi có chứa pom.xml (service)
        CHANGED_SERVICES = ''
    }

    stage('Detect Changed Services') {
    steps {
        script {
            checkout([
                $class: 'GitSCM',
                branches: [[name: "*/${env.BRANCH_NAME}"]],
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'CleanBeforeCheckout']],
                userRemoteConfigs: [[url: 'https://github.com/DatNewBie/spring-petclinic-microservices.git']]
            ])

            // Lấy toàn bộ branch từ remote (bao gồm main)
            sh 'git fetch origin main'

            // So sánh file thay đổi giữa main và nhánh hiện tại
            def changedFiles = sh(
                script: "git diff --name-only origin/main...HEAD",
                returnStdout: true
            ).trim().split('\n')

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

