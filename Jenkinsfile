pipeline {
    agent any

    environment {
        APP_SERVER = "10.0.1.10"
        APP_USER = "ubuntu"
        APP_DIR = "/home/ubuntu/todolist"
    }

    stages {
        stage('Deploy') {
            steps {
                sshagent(credentials: ['app-ec2-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${APP_USER}@${APP_SERVER} '
                      cd ${APP_DIR} &&
                      git -C backend pull &&
                      git -C frontend pull &&
                      docker compose down &&
                      docker compose up -d --build --remove-orphans &&
                      docker image prune -f
                    '
                    """
                }
            }
        }

        stage('Health Check') {
            steps {
                sshagent(credentials: ['app-ec2-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${APP_USER}@${APP_SERVER} '
                      curl -f http://localhost/health &&
                      curl -f http://localhost/ready &&
                      docker ps
                    '
                    """
                }
            }
        }
    }
}
