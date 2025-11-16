pipeline {
    agent none

    stages {
        stage('Build Go App') {
            agent {
                docker {
                    image 'golang:1.21'
                    reuseNode true
                }
            }

            steps {
                sh 'go build -o app .'
                echo 'SUCCESS'
            }
        }

        stage('Test Go App') {
            agent {
                docker {
                    image 'golang:1.21'
                    reuseNode true
                }
            }

            steps {
                sh 'go test -v ./...'
            }
        }

        stage('Lint') {
            agent {
                docker {
                    image 'golang:1.21'
                    reuseNode true
                }
            }

            steps {
                sh 'go fmt ./...'
                sh 'go vet ./...'
            }
        }

        stage('Package Info') {
            agent {
                docker {
                    image 'alpine:latest'
                }
            }

            steps {
                sh 'cat /etc/alpine-release'
                sh 'find . -type f | wc -l'
            }
        }
    }
}