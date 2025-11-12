pipeline {
    agent any

    stages {
        stage('Inspect Workspace') {
            steps {
                dir('pwd') {
                    sh 'ls -la'
                    sh 'du -sh .'
                    echo "Workspace: ${env.WORKSPACE}"
                }
            }
        }

        stage('Create Test Files') {
            steps {
                sh 'echo "test" > test1.txt'
                sh 'mkdir -p temp && echo "temp" > temp/temp.log'
                sh 'mkdir -p old-build && echo "old" > old-build/app.jar'
                sh 'ls -R'
            }
        }
    }
}