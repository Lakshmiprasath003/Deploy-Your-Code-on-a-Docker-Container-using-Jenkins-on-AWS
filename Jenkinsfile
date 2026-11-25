pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Getting source code'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image'
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application'
            }
        }

        stage('Health Check') {
            steps {
                echo 'Checking application'
            }
        }
    }
}