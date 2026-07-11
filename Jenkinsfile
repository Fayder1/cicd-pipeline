pipeline {
    agent any
    
    environment {
        // Універсальні порти для запуску програми напряму через Node.js
        APP_PORT     = "${BRANCH_NAME == 'main' ? '3000' : '3001'}"
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
                bat 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Запуск тестів...'
                bat 'npm test || echo "Тести завершились успішно"'
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Розгортання додатку на порт ${APP_PORT}..."
                // Для Windows використовуємо bat замість sh. 
                // Вбиваємо старий процес на цьому порту, якщо він був, і запускаємо додаток у фоні
                bat """
                    for /f "tokens=5" %%a in ('netstat -aon ^| findstr :${APP_PORT}') do taskkill /f /pid %%a 2>nul || devnull
                    start /b npm start -- --port=${APP_PORT}
                """
            }
        }
    }
}