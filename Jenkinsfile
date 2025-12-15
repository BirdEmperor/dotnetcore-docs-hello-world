pipeline {
    agent any
    environment {
        DEPLOY_PATH = "/var/www/dotnet-app"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                // Собираем проект
                sh 'dotnet publish -c Release -o ./publish'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['ssh-local']) {
                    // Останавливаем, чистим, копируем, запускаем
                    sh "ssh -o StrictHostKeyChecking=no jenkins@localhost 'sudo systemctl stop webapp || true'"
                    sh "ssh -o StrictHostKeyChecking=no jenkins@localhost 'rm -rf ${DEPLOY_PATH}/*'"
                    sh "scp -o StrictHostKeyChecking=no -r ./publish/* jenkins@localhost:${DEPLOY_PATH}"
                    sh "ssh -o StrictHostKeyChecking=no jenkins@localhost 'sudo systemctl start webapp'"
                }
            }
        }
    }
}
