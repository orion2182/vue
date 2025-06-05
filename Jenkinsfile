pipeline {
    agent any
    
    environment {
        APP_NAME = 'laravel-vue-app'
        APP_PATH = '/var/www/laravel-vue-app'
        NODE_VERSION = 'NodeJS 18.20.8'
    }
    
    stages {
        stage('Setup') {
            steps {
                script {
                    nodejs(nodeJSInstallationName: "${NODE_VERSION}") {
                        sh 'node --version'
                        sh 'npm --version'
                    }
                }
            }
        }

        stage('Checkout') {
            steps {
                echo 'Source code already checked out by Jenkins.'
            }
        }
        
        stage('Install PHP Dependencies') {
            steps {
                sh 'composer install --no-dev --prefer-dist --optimize-autoloader'
            }
        }
        
        stage('Install Node Dependencies') {
            steps {
                script {
                    nodejs(nodeJSInstallationName: "${NODE_VERSION}") {
                        sh 'npm install'
                    }
                }
            }
        }
        
        stage('Build Assets') {
            steps {
                script {
                    nodejs(nodeJSInstallationName: "${NODE_VERSION}") {
                        sh 'npm run prod'
                    }
                }
            }
        }
        
        stage('Setup Environment') {
            steps {
                sh 'cp .env.example .env || true'
                sh 'php artisan key:generate'
            }
        }
        
        stage('Deploy') {
            steps {
                sh """
                    rm -rf ${APP_PATH}/*
                    cp -R . ${APP_PATH}
                    chmod -R 775 ${APP_PATH}/storage
                    chmod -R 775 ${APP_PATH}/bootstrap/cache
                """
                
                dir("${APP_PATH}") {
                    sh 'php artisan cache:clear'
                    sh 'php artisan config:cache'
                    sh 'php artisan route:cache'
                    sh 'php artisan view:cache'
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
