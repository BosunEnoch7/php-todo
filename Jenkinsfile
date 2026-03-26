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

        stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'phploc.csv',
                     csvSeries: [[displayTableFlag: false, exclusionValues: '', file: 'build/logs/phploc.csv', inclusionFlag: 'OFF', series: 'Classes', url: '']],
                     group: 'Code Analysis',
                     numBuilds: '',
                     style: 'line',
                     title: 'Classes'

                plot csvFileName: 'phploc.csv',
                     csvSeries: [[displayTableFlag: false, exclusionValues: '', file: 'build/logs/phploc.csv', inclusionFlag: 'OFF', series: 'Methods', url: '']],
                     group: 'Code Analysis',
                     numBuilds: '',
                     style: 'line',
                     title: 'Methods'

                plot csvFileName: 'phploc.csv',
                     csvSeries: [[displayTableFlag: false, exclusionValues: '', file: 'build/logs/phploc.csv', inclusionFlag: 'OFF', series: 'Namespaces', url: '']],
                     group: 'Code Analysis',
                     numBuilds: '',
                     style: 'line',
                     title: 'Namespaces'

                plot csvFileName: 'phploc.csv',
                     csvSeries: [[displayTableFlag: false, exclusionValues: '', file: 'build/logs/phploc.csv', inclusionFlag: 'OFF', series: 'Lines of Code', url: '']],
                     group: 'Code Analysis',
                     numBuilds: '',
                     style: 'line',
                     title: 'Lines of Code'
            }
        }
    }
}
