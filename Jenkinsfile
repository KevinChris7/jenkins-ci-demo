pipeline {
    agent any

    environment {
        IMAGE_NAME = "kevinchris7/jenkins-docker-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    def scmVars = checkout scm
                    env.GIT_COMMIT_SHORT = scmVars.GIT_COMMIT.take(7)
                    env.BUILD_TAG_VERSION = env.BUILD_NUMBER
                    env.LATEST_TAG = "latest"
                }
            }
        }

        stage('Show Tag Values') {
            steps {
                echo "Build Number Tag: ${BUILD_TAG_VERSION}"
                echo "Git Commit Tag: ${GIT_COMMIT_SHORT}"
                echo "Latest Tag: ${LATEST_TAG}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${IMAGE_NAME}:${BUILD_TAG_VERSION} .
                    docker tag ${IMAGE_NAME}:${BUILD_TAG_VERSION} ${IMAGE_NAME}:${GIT_COMMIT_SHORT}
                    docker tag ${IMAGE_NAME}:${BUILD_TAG_VERSION} ${IMAGE_NAME}:${LATEST_TAG}
                """
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-dev', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Push Docker Tags') {
            steps {
                sh """
                    docker push ${IMAGE_NAME}:${BUILD_TAG_VERSION}
                    docker push ${IMAGE_NAME}:${GIT_COMMIT_SHORT}
                    docker push ${IMAGE_NAME}:${LATEST_TAG}
                """
            }
        }

        stage('Verify Local Images') {
            steps {
                sh 'docker images | grep jenkins-docker-demo || true'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
