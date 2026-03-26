stage('Code Analysis') {
    steps {
        sh 'mkdir -p build/logs'
        sh 'phploc app/ --log-csv build/logs/phploc.csv'
    }
}
