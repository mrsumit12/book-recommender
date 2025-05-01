pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'sumit7372'
        FRONTEND_IMAGE = "${DOCKER_HUB_USER}/book-frontend"
        BACKEND_IMAGE = "${DOCKER_HUB_USER}/book-backend"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/mrsumit12/book-recommender'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${FRONTEND_IMAGE}", 'frontend')
                    docker.build("${BACKEND_IMAGE}", 'backend')
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                    script {
                        docker.image("${FRONTEND_IMAGE}").push("latest")
                        docker.image("${BACKEND_IMAGE}").push("latest")
                    }
                }
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                    docker rm -f frontend || true
                    docker rm -f backend || true

                    docker run -d --name frontend -p 90:90 ${FRONTEND_IMAGE}:latest
                    docker run -d --name backend -p 5000:5000 ${BACKEND_IMAGE}:latest
                '''
            }
        }
    }

    post {
        always {
            echo '✅ CI/CD Pipeline Finished'
        }
    }
}
