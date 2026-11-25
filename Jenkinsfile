
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'lakshmiprasath03/deploy-jenkins-aws'
        CONTAINER_NAME = 'laks-dashboard'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'GitHub source code checked out'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                      -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                      -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh '''
                        set +x
                        echo "$DOCKERHUB_PASSWORD" | docker login \
                          --username "$DOCKERHUB_USERNAME" \
                          --password-stdin

                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                        docker push ${IMAGE_NAME}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} 2>/dev/null || true

                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      -p 80:8080 \
                      --restart unless-stopped \
                      ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    for attempt in $(seq 1 45); do
                        STATUS=$(docker inspect \
                          --format='{{.State.Health.Status}}' \
                          ${CONTAINER_NAME} 2>/dev/null || true)

                        echo "Container health: $STATUS"

                        if [ "$STATUS" = "healthy" ]; then
                            echo "Application is healthy!"
                            exit 0
                        fi

                        sleep 2
                    done

                    docker logs ${CONTAINER_NAME}
                    echo "Health check failed!"
                    exit 1
                '''
            }
        }
    }
}
