pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'nyashinalex'
        IMAGE_NAME = 'go-app'
        IMAGE_TAG = "1.0.${env.BUILD_NUMBER}"
        FULL_IMAGE_NAME = "${DOCKER_HUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        BUILD_TIME = "${sh(script: 'date -u +"%Y-%m-%dT%H:%M:%SZ"', returnStdout: true).trim()}"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    customImage = docker.build(
                        "${env.FULL_IMAGE_NAME}",
                        "-f ./go-app/Dockerfile " +
                        "--build-arg APP_VERSION=${env.IMAGE_TAG} " +
                        "--build-arg BUILD_TIME=${env.BUILD_TIME} ./go-app"
                    )
                }
            }
        }

        stage('Test Image') {
            steps {
                script {
                    customImage.inside {
                        sh 'pwd'
                        sh 'ls -lh /app/app'
                        sh 'chmod +x /app/app'
                        sh 'nohup /app/app > app.log 2>&1 &'
                        sh 'sleep 2'
                        sh 'wget --spider http://localhost:8080/health'
                        sh 'wget -qO- http://localhost:8080/info'
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        customImage.push("${env.IMAGE_TAG}")
                        customImage.push('latest')
                    }
                }
            }
        }

        stage('Verify Registry') {
            steps {
                script {
                    sh "docker rmi ${env.FULL_IMAGE_NAME}"
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        def image = docker.image("${env.FULL_IMAGE_NAME}")
                        image.pull()
                        sh "docker images ${env.DOCKER_HUB_USERNAME}/${env.IMAGE_NAME}"
                    }
                }
            }
        }

        stage('Tag Additional Versions') {
            when {
                branch 'main'
            }

            steps {
                script {
                    customImage.push('stable')
                }
            }
        }
    }

    post {
        always {
            sh "docker rmi ${env.DOCKER_HUB_USERNAME}/${env.IMAGE_NAME}:${env.IMAGE_TAG} || true"
            sh "docker rmi ${env.DOCKER_HUB_USERNAME}/${env.IMAGE_NAME}:latest || true"
            sh 'docker image prune -f'
        }
    }
}