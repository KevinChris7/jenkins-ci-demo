pipeline {

    agent any

    environment {
        IMAGE_NAME = "kevinchris7/jenkins-docker-demo"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        EC2_HOST   = "13.203.67.221"
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
                withCredentials([usernamePassword(credentialsId: 'docker-hub-dev', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {

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
                        docker pull ${IMAGE_NAME}:${IMAGE_TAG}
                    '
                    '''
                }
            }
        }

    }

}
