pipeline {
    agent any

    environment {
        // ЗАМЕНИТЕ НА ВАШ ЛОГИН DOCKER HUB
        DOCKER_IMAGE = 'your_dockerhub_username/laravel-app' 
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/artem12gbq/my-laravel-progect.git'
            }
        }

        stage('Setup Environment') {
            steps {
                script {
                    // Создаем .env из примера, если нет production версии
                    sh 'cp .env.example .env || true'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Собираем образ. Убедитесь, что Dockerfile в корне проекта!
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Этот этап сработает, только если вы создали Credentials в Jenkins
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_IMAGE}:${BUILD_NUMBER}").push()
                        docker.image("${DOCKER_IMAGE}:latest").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Простейший запуск контейнера (без docker-compose для начала)
                    sh "docker stop laravel-app || true"
                    sh "docker rm laravel-app || true"
                    sh "docker run -d -p 80:80 --name laravel-app ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                }
            }
        }
    }

    post {
        success {
            echo 'Laravel application deployed successfully!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
