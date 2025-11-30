// Обязательно! Чтобы customImage был доступен во всех stages и в post.
def customImage = null

pipeline {
    agent any

    parameters {
        choice(name: 'DEPLOY_ENV', choices: ['dev', 'staging', 'production'])
        booleanParam(name: 'SKIP_TESTS', defaultValue: false)
        string(name: 'DOCKER_REGISTRY', defaultValue: 'registry.company.com')
    }

    environment {
        APP_NAME = 'flask-app'
        VERSION = "1.0.${BUILD_NUMBER}"
        IMAGE_TAG = "${params.DEPLOY_ENV}-${VERSION}"
    }

    stages {
        stage('Prepare Environment') {
            steps {
                sh "cat configs/config.${params.DEPLOY_ENV}.env"
            }
        }

        stage('Build') {
            steps {
                dir('flask-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    echo "BUILD_VERSION: ${env.VERSION}"
                }
            }
        }

        stage('Test') {
            when {
                expression { !params.SKIP_TESTS }
            }
            steps {
                dir('flask-app') {
                    sh 'pytest test_app.py -v'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    customImage = docker.build(
                        "${params.DOCKER_REGISTRY}/${env.APP_NAME}:${env.IMAGE_TAG}",
                        "--build-arg APP_VERSION=${env.BUILD_NUMBER} " +
                        "--build-arg BUILD_TIME=${System.currentTimeMillis()} " +
                        "--build-arg GIT_COMMIT=${env.GIT_COMMIT} " +
                        "--build-arg GIT_BRANCH=${env.GIT_BRANCH} ./flask-app"
                    )
                }
            }
        }

        stage('Deploy') {
            steps {
                script {

                    // PRODUCTION подтверждение
                    if (params.DEPLOY_ENV == 'production') {
                        input message: "Deploy to PRODUCTION?"
                    }

                    echo "Deploying to ${params.DEPLOY_ENV}"

                    // Удаляем старый контейнер
                    sh """
                        docker stop ${env.APP_NAME} || true
                        docker rm ${env.APP_NAME} || true
                    """

                    // Запуск нового контейнера
                    sh """
                        docker run -d -p 5000:5000 --name ${env.APP_NAME} \
                        ${params.DOCKER_REGISTRY}/${env.APP_NAME}:${env.IMAGE_TAG}
                    """

                    // Читаем API_URL из env файла
                    env.API_URL = sh(
                        script: "grep '^API_URL=' configs/config.${params.DEPLOY_ENV}.env | cut -d '=' -f2-",
                        returnStdout: true
                    ).trim()
                    echo "API_URL = ${env.API_URL}"

                    echo "Build duration: ${currentBuild.durationString}"
                }
            }
        }

        stage('Health Check') {
            steps {
                sh 'sleep 20'
                sh "curl -f http://172.17.0.1:5000/health"
            }
        }
    }

    post {
        success {
            echo "SUCCESS deploy"
            echo "ENV = ${params.DEPLOY_ENV}"
            echo "VERSION = ${env.VERSION}"
            echo "Build took: ${currentBuild.durationString}"
        }

        failure {
            echo "❌ ERROR in ${params.DEPLOY_ENV} environment"
        }

        always {
            script {
                // Останавливаем контейнер, если он ещё работает
                sh "docker stop ${env.APP_NAME} || true"
                sh "docker rm ${env.APP_NAME} || true"
            }
        }
    }
}