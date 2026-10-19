pipeline {

    agent any

    environment {
        IMAGE_NAME = "your-dockerhub-username/jenkins-docker-demo"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        EC2_HOST   = "your-ec2-public-ip"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-deploy-key']) {

                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_HOST} '
                        docker pull ${IMAGE_NAME}:${IMAGE_TAG} &&
                        docker stop app-container || true &&
                        docker rm app-container || true &&
                        docker run -d -p 80:80 --name app-container ${IMAGE_NAME}:${IMAGE_TAG}
                    '
                    '''
                }
            }
        }

    }

}
