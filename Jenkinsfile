pipeline {
    agent any

    environment {
        APP_VERSION = "1.0.${env.BUILD_NUMBER}"
        BUILD_TIME = "${sh(script: 'date -u +"%Y-%m-%dT%H:%M:%SZ"', returnStdout: true).trim()}"
        GIT_COMMIT = "${sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()}"
    }

    stages {
        stage('Build Go App') {
            steps {
                script {
                    customImage = docker.build(
                        "go-app:${env.BUILD_NUMBER}",
                        "-f ./go-app/Dockerfile " +
                        "--build-arg APP_VERSION=${env.APP_VERSION} " +
                        "--build-arg BUILD_TIME=${env.BUILD_TIME} " +
                        "--build-arg GIT_COMMIT=${env.GIT_COMMIT} ./go-app"
                    )
                }
            }
        }

        stage('Test in Container') {
            steps {
                script {
                    customImage.inside {
                    sh 'pwd'
                    sh 'ls -lh /app/app'
                    sh 'chmod +x /app/app'
                    sh 'nohup /app/app > app.log 2>&1 &'
                    sh 'sleep 2'
                    sh 'wget -O- http://localhost:8080/health'
                    }
                }
            }
        }

        stage('Image Info') {
            steps {
                sh "docker images go-app:${env.BUILD_NUMBER} --format \"{{.Size}}\""
                sh "docker images go-app:${env.BUILD_NUMBER} --format \"{{.ID}}\""
                sh "docker images go-app:${env.BUILD_NUMBER} --format \"{{.CreatedAt}}\""
            }
        }
    }

    post {
        always {
            sh "docker rmi go-app:${env.BUILD_NUMBER} || true"
            sh 'docker image prune -f'
        }
    }
}