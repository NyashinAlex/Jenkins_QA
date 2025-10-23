pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                echo 'Git branch'
                sh 'git branch --show-current'
            }
        }

        stage('Deploy to Production') {
            steps {
                when {
                    branch 'main'
                }
                echo 'Deploying to production environment'
                echo 'Branch: main - deployment allowed'
            }
        }
    }
}