pipeline {
    agent any

    environment {
        env.DEPLOY_ENV = 'staging'
    }

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

        stage('Deploy to Staging') {
            steps {
                when {
                    env.DEPLOY_ENV = 'staging'
                }
                echo 'Deploying to staging environment'
                echo "Environment: ${env.DEPLOY_ENV}"
            }
        }

        stage('Deploy to Production (by env)') {
            steps {
                when {
                    env.DEPLOY_ENV = 'production'
                }
                echo 'Deploying to production environment'
                echo "Environment: ${env.DEPLOY_ENV}"
            }
        }

        stage('Run Tests') {
            steps {
                when {
                    expression {
                        env.BUILD_NUMBER.toInteger() % 2 == 0
                    }
                }
                echo "Running tests for build ${env.BUILD_NUMBER}"
                echo 'This is an even-numbered build'
            }
        }

        stage('Skip Tests') {
            steps {
                when {
                    expression {
                        env.BUILD_NUMBER.toInteger() % 2 != 0
                    }
                }
                echo "Skipping tests for build ${env.BUILD_NUMBER}"
                echo 'This is an odd-numbered build'
            }
        }
    }
}