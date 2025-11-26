pipeline {
    agent any

    environment {
        APP_NAME = 'flask-app'
        VERSION = "1.0.${BUILD_NUMBER}"
        IMAGE_TAG = "${env.BRANCH_NAME}-${env.VERSION}"
    }

    stages {
        stage('Branch Info') {
            steps {
                echo """
                BRANCH_NAME = ${env.BRANCH_NAME}
                IMAGE_TAG = ${env.IMAGE_TAG}
                TYPE = production
                """
            }
        }

        stage('Build') {
            steps {
                dir('flask-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    echo "BUILD_VERSION: ${env.BUILD_VERSION}"
                }
            }
        }

        stage('Test') {
            steps {
                dir('flask-app') {
                    sh 'pytest test_app.py -v'
                }
            }
        }

        stage('Build Docker Image') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                    branch pattern: "feature/.*", comparator: "REGEXP"
                }
            }

            steps {
                script {
                    customImage = docker.build(
                        "my-app:${env.BUILD_NUMBER}",
                        "--build-arg APP_VERSION=${env.BUILD_NUMBER} " +
                        "--build-arg BUILD_TIME=${System.currentTimeMillis()} " +
                        "--build-arg GIT_COMMIT=${env.GIT_COMMIT} " +
                        "--build-arg GIT_BRANCH=${env.GIT_BRANCH} ./go-app"
                    )
                }
            }
        }

        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }

            steps {
                echo 'Deploying to staging environment'
                echo 'run deploy'
            }
        }

        stage('Deploy to Production') {
            steps {
                input message: 'Deploy to production?'
                echo 'Deploy from production'
            }
        }
    }

    post {
        success {
            echo "SUCCESS for ${env.BRANCH_NAME}"
        }
        failure {
            script {
                switch (env.BRANCH_NAME) {
                    case 'main':
                        echo "FAIL for main, fix!"
                        break

                    case 'develop':
                        echo "FAIL for develop, QA update"
                        break

                    default:
                        if (env.BRANCH_NAME.startsWith('feature/')) {
                            echo "FAIL for ${env.BRANCH_NAME}, good!"
                        } else {
                            echo "FAIL for ${env.BRANCH_NAME}"
                        }
                }
            }
        }
    }
}