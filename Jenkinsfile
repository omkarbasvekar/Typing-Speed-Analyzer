pipeline {
    agent any

    environment {
        DOCKER_HOST_IP = "98.80.0.197"
        DOCKER_USER = "ubuntu"
        DOCKER_APP_DIR = "chat-app"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/omkarbasvekar/Typing-Speed-Analyzer.git'
            }
        }

        stage('Build Docker Image') {
    steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'KEY')]) {
            sh """
                ssh -i \$KEY -o StrictHostKeyChecking=no ${DOCKER_USER}@${DOCKER_HOST_IP} '
                    rm -rf ${DOCKER_APP_DIR} && mkdir -p ${DOCKER_APP_DIR}
                '

                scp -i \$KEY -o StrictHostKeyChecking=no -r \
                    frontend\
                    ${DOCKER_USER}@${DOCKER_HOST_IP}:${DOCKER_APP_DIR}/

                ssh -i \$KEY -o StrictHostKeyChecking=no ${DOCKER_USER}@${DOCKER_HOST_IP} '
                    cd ${DOCKER_APP_DIR}/frontend &&
                    docker build -t vite-chat-app .
                '
            """
        }
    }
}


        stage('Run Container') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'KEY')]) {
                    sh """
                        ssh -i \$KEY -o StrictHostKeyChecking=no ${DOCKER_USER}@${DOCKER_HOST_IP} '
                            docker rm -f vite-chat-container || true &&
                            docker run -d -p 80:80 --name vite-chat-container vite-chat-app
                        '
                    """
                }
            }
        }

        stage('Selenium Tests') {
            steps {
                sh """
                    echo "Running Selenium tests..."
                    # TODO: Add your Selenium test command here
                """
            }
        }
    }
}
