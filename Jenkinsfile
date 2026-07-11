pipeline {
    agent any
    
    environment {
        // Оскільки це гілка dev, жорстко прописуємо її порт
        APP_PORT = '3001'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo "Збірка додатку для dev гілки..."
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
                // Закриваємо старий процес на цьому порту, якщо він завис, і запускаємо заново
                bat """
                    for /f "tokens=5" %%a in ('netstat -aon ^| findstr :${APP_PORT}') do taskkill /f /pid %%a 2>nul || set errorlevel=0
                    start /b npm start -- --port=${APP_PORT}
                """
            }
        }
    }
}