pipeline {

    agent any

    stages {

        stage('Clone Repository') {
            steps {
                echo "Cloning repository"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-docker-demo .'
            }
        }

        stage('List Docker Images') {
            steps {
                sh 'docker images'
            }
        }

    }

}
