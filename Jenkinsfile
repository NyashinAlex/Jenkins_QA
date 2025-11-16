pipeline {
    agent none

    stages {
        stage('Build Go App') {
            agent {
                docker {
                    image 'golang-docker-cli:1.21'
                    args '-v /var/run/docker.sock:/var/run/docker.sock --user root'
                    reuseNode true
                }
            }

            steps {
                dir('go-app') {
                    sh 'go build -buildvcs=false -o app .'
                }
                echo 'SUCCESS'
            }
        }

        stage('Test Go App') {
            agent {
                docker {
                    image 'golang-docker-cli:1.21'
                    args '-v /var/run/docker.sock:/var/run/docker.sock --user root'
                    reuseNode true
                }
            }

            steps {
                dir('go-app') {
                    sh 'go test -v ./...'
                }
            }
        }

        stage('Lint') {
            agent {
                docker {
                    image 'golang-docker-cli:1.21'
                    args '-v /var/run/docker.sock:/var/run/docker.sock --user root'
                    reuseNode true
                }
            }

            steps {
                dir('go-app') {
                    sh 'go fmt ./...'
                    sh 'go vet ./...'
                }
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