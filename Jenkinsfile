pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                echo 'Preparing workspace...'
                sh 'mkdir -p build logs temp'
                echo 'Directories created'
            }
        }
    }
}