
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

        stage('Docker Push & Pull Verification') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh '''
                        set +x
                        trap 'docker logout >/dev/null 2>&1 || true' EXIT

                        echo "$DOCKERHUB_PASSWORD" | docker login \
                          --username "$DOCKERHUB_USERNAME" \
                          --password-stdin

                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                        docker push ${IMAGE_NAME}:latest

                        echo "Verifying image pull from Docker Hub..."
                        docker pull ${IMAGE_NAME}:${BUILD_NUMBER}

                        echo "Docker Hub pull verification successful!"
                    '''
                }
            }
        }

        stage('Deploy with Rollback') {
            steps {
                sh '''
                    set -eu

                    BACKUP="${CONTAINER_NAME}-rollback-${BUILD_NUMBER}"
                    OLD_CONTAINER_EXISTS=false

                    rollback() {
                        RESULT=$?
                        trap - EXIT

                        if [ "$RESULT" -ne 0 ]; then
                            echo "Deployment failed. Starting rollback..."

                            docker rm -f "$CONTAINER_NAME" 2>/dev/null || true

                            if [ "$OLD_CONTAINER_EXISTS" = "true" ]; then
                                docker rename "$BACKUP" "$CONTAINER_NAME"
                                docker start "$CONTAINER_NAME"
                                echo "Previous container restored!"
                            else
                                echo "No previous container available."
                            fi
                        else
                            if [ "$OLD_CONTAINER_EXISTS" = "true" ]; then
                                docker rm -f "$BACKUP"
                            fi

                            echo "Deployment successful!"
                        fi

                        exit "$RESULT"
                    }

                    trap rollback EXIT

                    if docker inspect "$CONTAINER_NAME" >/dev/null 2>&1; then
                        OLD_CONTAINER_EXISTS=true

                        echo "Saving previous working container..."
                        docker stop "$CONTAINER_NAME"
                        docker rename "$CONTAINER_NAME" "$BACKUP"
                    fi

                    echo "Starting new application container..."

                    docker run -d \
                      --name "$CONTAINER_NAME" \
                      --memory=512m \
                      --cpus=0.50 \
                      -p 80:8080 \
                      --restart unless-stopped \
                      ${IMAGE_NAME}:${BUILD_NUMBER}

                    echo "Checking container health..."

                    for attempt in $(seq 1 45); do
                        STATUS=$(docker inspect \
                          --format='{{.State.Health.Status}}' \
                          "$CONTAINER_NAME" 2>/dev/null || true)

                        echo "Health status: $STATUS"

                        if [ "$STATUS" = "healthy" ]; then
                            break
                        fi

                        if [ "$attempt" -eq 45 ]; then
                            docker logs "$CONTAINER_NAME"
                            echo "Health check failed!"
                            exit 1
                        fi

                        sleep 2
                    done

                    echo "Running application curl validation..."

                    for attempt in $(seq 1 10); do
                        if curl -fsS http://127.0.0.1/ >/dev/null; then
                            echo "Application curl validation successful!"
                            break
                        fi

                        if [ "$attempt" -eq 10 ]; then
                            echo "Application curl validation failed!"
                            exit 1
                        fi

                        sleep 2
                    done
                '''
            }
        }
    }
}
