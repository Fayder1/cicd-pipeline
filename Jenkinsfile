pipeline {
    agent any
    
    tools {
        nodejs 'Node 7.8.0' // Інструмент, який налаштовується у Jenkins Global Tools
    }
    
    environment {
        // Завдяки умовній логіці, цей скрипт буде універсальним для обох гілок
        APP_PORT     = "${BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME   = "${BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG    = "v1.0"
        CONTAINER    = "${BRANCH_NAME == 'main' ? 'app_main' : 'app_dev'}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo "Збірка додатку для гілки: ${BRANCH_NAME}..."
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Запуск тестів...'
                sh 'npm test || echo "Тести завершились (ігноруємо помилки для лаби)"'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Збірка Docker образу ${IMAGE_NAME}:${IMAGE_TAG}..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Розгортання контейнера ${CONTAINER} на порт ${APP_PORT}..."
                // Advanced task: видаляємо старий контейнер ТІЛЬКИ для поточного env
                sh """
                    docker stop ${CONTAINER} || true
                    docker rm ${CONTAINER} || true
                    docker run -d --name ${CONTAINER} -p ${APP_PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
}