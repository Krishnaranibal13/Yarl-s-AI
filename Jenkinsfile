pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'docker-compose.yml'
        COMPOSE_PROJECT_NAME = 'yarls-ai'
    }

    stages {

        stage('Check Files') {
            steps {
                sh '''
                    echo "Workspace:"
                    pwd

                    echo "Project files:"
                    ls -la

                    echo "Checking required files..."

                    test -f docker-compose.yml
                    test -d backend
                    test -d front-end

                    echo "All required files found."
                '''
            }
        }

        stage('Stop Existing Application') {
            steps {
                sh '''
                    docker compose -f ${COMPOSE_FILE} down || true
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                    docker compose -f ${COMPOSE_FILE} build
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

                    echo ""
                    echo "Docker containers:"
                    docker ps --format "table {{.Names}}\\t{{.Status}}\\t{{.Ports}}"
                '''
            }
        }

        stage('Application Test') {
            steps {
                sh '''
                    echo "Testing application..."

                    curl -f http://localhost/ || exit 1

                    echo "Application is responding successfully."
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
    }
}
