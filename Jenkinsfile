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
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'SonarQubeScanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=php-todo \
                          -Dsonar.projectName=php-todo \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=http://3.87.215.227:9000
                    """
                }
            }
        }

        stage('SonarQube Quality Gate') {
            when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP" }
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
                error('Stopping pipeline here intentionally to match guide visual.')
            }
        }

        stage('Package Artifact') {
            steps {
                sh """
                    cd ${WORKSPACE}
                    zip -qr php-todo.zip .
                    ls -lh php-todo.zip
                """
            }
        }

        stage('Upload Artifact to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server('artifactory-server')
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "php-todo.zip",
                                "target": "generic-local/php-todo/",
                                "props": "type=zip;status=ready"
                            }
                        ]
                    }"""
                    server.upload spec: uploadSpec
                }
            }
        }

        stage('Deploy to Dev Environment') {
            steps {
                build job: 'ansible-config-mgt/main',
                    parameters: [
                        [$class: 'StringParameterValue', name: 'env', value: 'dev']
                    ],
                    propagate: true,
                    wait: true
            }
        }
    }
}
