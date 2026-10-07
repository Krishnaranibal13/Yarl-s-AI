pipeline {
    agent any

    environment {
        PROJECT_NAME = 'yarls-ai'
        COMPOSE_PROJECT_NAME = 'yarls-ai'
        COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Files') {
            steps {
                sh '''
                    echo "Workspace:"
                    pwd

                    echo "Project files:"
                    ls -la

                    echo "Docker Compose:"
                    test -f docker-compose.yml

                    echo "Backend:"
                    test -d backend

                    echo "Frontend:"
                    test -d front-end
                '''
            }
        }

        stage('Stop Existing Containers') {
            steps {
                sh '''
                    docker compose -f ${COMPOSE_FILE} down || true
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                    docker compose -f ${COMPOSE_FILE} build --no-cache
                '''
            }
        }

        stage('Start Application') {
            steps {
                sh '''
                    docker compose -f ${COMPOSE_FILE} up -d
                '''
            }
        }

        stage('Check Containers') {
            steps {
                sh '''
                    sleep 10

                    docker compose -f ${COMPOSE_FILE} ps

                    echo "Running containers:"
                    docker ps --format "table {{.Names}}\\t{{.Status}}\\t{{.Ports}}"
                '''
            }
        }

        stage('Application Test') {
            steps {
                sh '''
                    echo "Testing frontend..."
                    curl -f http://localhost/ || exit 1

                    echo "Frontend is responding."
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
                docker compose -f ${COMPOSE_FILE} ps || true
                docker compose -f ${COMPOSE_FILE} logs --tail=100 || true
            '''
        }

        always {
            echo 'Deployment pipeline finished.'
        }
    }
}
