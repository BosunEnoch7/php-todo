pipeline {
    agent any

    stages {
        stage('Initial Cleanup') {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/BosunEnoch7/php-todo.git'
            }
        }

        stage('Prepare Dependencies') {
            steps {
                sh 'cp .env.sample .env || true'
                sh '''
                docker run --rm \
                  -v "$WORKSPACE":/app \
                  -w /app \
                  php:7.4-cli bash -c "
                    apt-get update &&
                    apt-get install -y unzip git curl &&
                    curl -sS https://getcomposer.org/installer | php &&
                    php composer.phar install
                  "
                '''
            }
        }
    }
}
