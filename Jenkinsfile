pipeline {
    // agent any: chạy trên agent mặc định (Jenkins có Docker socket thì stage con có thể dùng image Node)
    agent any

    // Tự kiểm tra Git theo lịch; có commit mới trên branch job đang theo -> tự chạy (không cần bấm Build)
    // Lần đầu sau khi thêm/sửa triggers: chạy Build Now một lần để Jenkins đăng ký lịch.
    // triggers {
    //   pollSCM('H/3 * * * *')
    // }

    options {
        // Giữ log build gọn, tránh treo pipeline
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                // Khi job lấy Jenkinsfile từ SCM, checkout thường đã có; giữ bước này cho rõ ràng / một số cấu hình đặc thù
                checkout scm
            }
        }

        // Dùng container Node để không cần cài Node trên máy Jenkins (cần plugin Docker Pipeline + quyền docker)
        stage('Install & Lint & Build') {
            agent {
                docker {
                    image 'node:22-alpine'
                    // Cùng workspace với bước checkout trên node Jenkins
                    reuseNode true
                }
            }
            steps {
                sh 'node -v && npm -v'
                sh 'npm ci'
                sh 'npm run lint'
                sh 'npm run build'
            }
        }
    }

    post {
        success {
            // Lưu thư mục build để tải về / xem trên giao diện Jenkins
            archiveArtifacts artifacts: 'dist/**/*', fingerprint: true, allowEmptyArchive: false
        }
        failure {
            echo 'Pipeline that bai! Check log stages'
        }
    }
}
