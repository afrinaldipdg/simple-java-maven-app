pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input message: 'Apakah Anda setuju untuk melanjutkan ke tahap Deploy? (Klik "Proceed" untuk mengizinkan)', parameters: [
                        [$class: 'BooleanParameterDefinition', name: 'APPROVAL', defaultValue: false, description: 'Klik "Proceed" untuk mengizinkan']
                    ]
                    if (!userInput.APPROVAL) {
                        error("Pengguna telah membatalkan proses. Tahap Deploy dibatalkan.")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                sleep time: 60, unit: 'SECONDS'
            }
        }
    }
}