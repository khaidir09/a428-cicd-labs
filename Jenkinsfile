pipeline {
    agent none // Tidak menjalankan agent di level global agar hemat resource saat menunggu approval
    
    stages {
        stage('Build') { 
            agent {
                docker { image 'node:16-buster-slim' }
            }
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            agent {
                docker { image 'node:16-buster-slim' }
            }
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }

        // Tahap Baru: Manual Approval
        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            }
        }

        stage('Deploy') {
            agent {
                docker { 
                    image 'node:16-buster-slim' 
                    args '-p 3000:3000'
                }
            }
            steps {
                sh './jenkins/scripts/deliver.sh'
                
                // Memberikan informasi di log Jenkins
                echo 'Aplikasi berhasil di-deploy. Menunggu selama 1 menit...'
                
                // Jeda otomatis selama 1 menit
                sleep time: 1, unit: 'MINUTES'

                // Menghentikan aplikasi setelah 1 menit
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}