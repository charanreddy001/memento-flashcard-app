pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = 'memento'
    }

    tools {
        nodejs 'NodeJS-18'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Pulling latest code from GitHub...'
                checkout scm
            }
        }

        stage('Install Server Dependencies') {
            steps {
                echo '📦 Installing server dependencies...'
                dir('server') {
                    bat 'npm install'
                }
            }
        }

        stage('Install Client Dependencies') {
            steps {
                echo '📦 Installing client dependencies...'
                dir('client') {
                    bat 'npm install'
                }
            }
        }

        stage('Build React App') {
            steps {
                echo '🏗️ Building React production build...'
                dir('client') {
                    bat 'npm run build'
                }
            }
        }

        stage('Stop Old Containers') {
            steps {
                echo '🛑 Stopping old Docker containers...'
                bat 'docker-compose down --remove-orphans'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo '🐳 Building Docker images...'
                bat 'docker-compose build --no-cache'
            }
        }

        stage('Deploy with Docker') {
            steps {
                echo '🚀 Starting all containers...'
                bat 'docker-compose up -d'
            }
        }

        stage('Health Check') {
            steps {
                echo '🏥 Checking if app is running...'
                sleep(time: 10, unit: 'SECONDS')
                bat 'docker ps'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline succeeded! App is live at http://localhost:3000'
        }
        failure {
            echo '❌ Pipeline failed! Check the logs above.'
            bat 'docker-compose logs --tail=50'
        }
        always {
            echo '📋 Pipeline finished.'
        }
    }
}