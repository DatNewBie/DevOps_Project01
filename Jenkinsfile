pipeline {
    agent any

    stages {
        stage('Detect Changes') {
            steps {
                script {
                    def changedFiles = getChangedFiles()
                    env.CHANGED_SERVICES = changedFiles.collect { 
                        // Giả sử service là thư mục chứa pom.xml
                        findServiceDir(it) 
                    }.unique().join(',')
                }
            }
        }

        stage('Build & Test') {
            when { expression { env.CHANGED_SERVICES?.trim() } }
            steps {
                script {
                    def services = env.CHANGED_SERVICES.split(',') as List
                    def parallelBuilds = [:]
                    
                    // Bước 1: Chạy test và generate report cho từng service
                    services.each { service ->
                        parallelBuilds[service] = {
                            dir(service) {
                                sh 'mvn clean verify -pl . -am' // Báo cáo Jacoco được tạo tại target/site/jacoco
                            }
                        }
                    }
                    parallel parallelBuilds
                    
                    // Bước 2: Tổng hợp tất cả report sau khi chạy song song
                    recordCoverage(
                        tools: [[parser: 'JACOCO']],
                        sourceFileResolver: [[projectDir: "$WORKSPACE"]], 
                        includes: services.collect { "$it/target/site/jacoco/jacoco.xml" }.join(',')
                    )
                }
            }
        }
    }
}

// Hàm helper tìm service từ file thay đổi
def findServiceDir(String filePath) {
    def path = new File(filePath)
    while (path != null) {
        if (new File(path, "pom.xml").exists()) {
            return path.toString()
        }
        path = path.parentFile
    }
    return null
}

// Hàm helper lấy danh sách file thay đổi
def getChangedFiles() {
    def changedFiles = []
    currentBuild.changeSets.each { changeSet ->
        changeSet.items.each { commit ->
            commit.affectedFiles.each { file ->
                changedFiles.add(file.path)
            }
        }
    }
    return changedFiles
}
