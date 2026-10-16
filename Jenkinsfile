pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code"
            }
        }

        stage('Build') {
            steps {
                echo "Building application"
                sh 'echo Build completed'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests"
                sh 'echo Tests executed successfully'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application"
                sh 'echo Deployment successful'
            }
        }

    }

}
