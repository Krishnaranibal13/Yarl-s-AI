
pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = 'yarls-ai'
    }

    stages {

        stage('Check Files') {
            steps {
                sh '''
                    echo "===== WORKSPACE ====="
                    pwd

                    echo "===== FILES ====="
                    ls -la

                    echo "===== CHECKING PROJECT ====="
                    test -f docker-compose.yml
                    test -d backend
                    test -d front-end

                    echo "Project files OK"
                '''
            }
        }

        stage('Docker Compose Down') {
            steps {
                sh '''
                    echo "Stopping existing application..."
                    docker compose down || true
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    echo "Building Docker images..."
                    docker compose build
                '''
            }
        }

        stage('Start Application') {
            steps {
                sh '''
                    echo "Starting application..."
                    docker compose up -d
                '''
            }
        }

        stage('Check Application') {
            steps {
                sh '''
                    echo "Waiting for containers..."
                    sleep 10

                    echo "===== CONTAINERS ====="
                    docker compose ps

                    echo "===== APPLICATION TEST ====="
                    curl -f http://localhost/ || exit 1

                    echo "Application is running successfully."
                '''
            }
        }
    }

    post {
        success {
            echo 'Yarl-s-AI deployment completed successfully.'
        }

        failure {
            echo 'Yarl-s-AI deployment failed.'

            sh '''
                echo "===== DOCKER STATUS ====="
                docker compose ps || true

                echo "===== CONTAINER LOGS ====="
                docker compose logs --tail=100 || true
            '''
        }

        always {
            echo 'Pipeline finished.'
        }
    }
}

