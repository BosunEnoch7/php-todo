pipeline {
    agent any

    stages {
        stage('Initial cleanup') {
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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t php-todo .'
            }
        }

        stage('Code Analysis') {
            steps {
                sh 'mkdir -p build/logs'
                sh 'phploc app/ --log-csv build/logs/phploc.csv'
            }
        }
    }
}
