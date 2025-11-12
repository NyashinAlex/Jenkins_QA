pipeline {
    agent any

    options {
        skipDefaultCheckout()
    }

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

        stage('Selective Clean') {
            steps {
                sh 'rm -rf temp/ old-build/ *.log'
                echo 'Cleaned temporary files and old builds'
                sh 'ls -la'
            }
        }

        stage('Build with Selective Clean') {
            steps {
                sh 'rm -rf python-app/dist/ python-app/build/'
                sh 'mkdir -p python-app/dist'
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    sh "python3 build.py"
                }
            }
        }

        stage('Full Clean') {
            steps {
                deleteDir()
                echo 'Workspace cleaned completely'
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
                echo 'Code checked out'
            }
        }

        stage('Build from Clean State') {
            steps {
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    sh "python3 build.py"
                }
            }
        }
    }

    post {
        always {
            sh 'du -sh .'
            cleanWs()
            echo 'Workspace cleaned after build'
        }
    }
}