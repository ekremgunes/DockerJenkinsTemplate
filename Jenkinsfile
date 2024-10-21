pipeline {
    agent any

    stages {
        stage('Stop & Remove Container') {
            steps {
                script {
                    // Mevcut konteyneri durdur ve sil
                    sh 'docker stop c_testapi || true'
                    sh 'docker rm c_testapi || true'
                }
            }
        }

        stage('Docker Compose Down') {
            steps {
                script {
                    // Varolan docker-compose servislerini durdur
                    sh 'docker compose -f ./DockerJenkinsTemplate/docker-compose.yml down || true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Docker image'ını build et, zaman aşımı kontrolü ile
                    sh 'timeout 300 docker compose -f ./DockerJenkinsTemplate/docker-compose.yml build --no-cache'
                    // Eğer zaman aşımına uğrarsa build işlemini sonlandır
                    sh '''
                    if [ $? -eq 124 ]; then
                        echo "Build işlemi zaman aşımına uğradı!"
                        exit 1
                    fi
                    '''
                }
            }
        }

        stage('Docker Compose Up') {
            steps {
                script {
                    // Docker container'ları başlat
                    sh 'docker compose -f ./DockerJenkinsTemplate/docker-compose.yml up -d'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline tamamlandı.'
        }
        failure {
            echo 'Pipeline başarısız oldu.'
        }
    }
}
