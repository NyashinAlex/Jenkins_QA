pipeline {
    agent any

    stages {
        stage('Variables Demo') {
            steps {
                script {
                    def appName = 'MyApplication'
                    def port = 8080
                    def isProduction = false
                    echo "App name, port and is_production: ${appName}, ${port}, ${isProduction}"
                }
            }
        }

        stage('String Operations') {
            steps {
                script {
                    def message = 'Jenkins Pipeline Tutorial'
                    echo "Size message: ${message.length()}"
                    echo "Upper case message: ${message.toUpperCase()}"
                    echo "Lower case message: ${message.toLowerCase()}"
                    echo "New message: ${message.replace('Tutorial', 'Course')}"
                }
            }
        }

        stage('Build Version') {
            steps {
                script {
                    def major = '1'
                    def minor = '0'
                    env.APP_VERSION = "1.0.${env.BUILD_NUMBER}"
                    echo "Application version: ${env.APP_VERSION}"
                }
            }
        }

        stage('Display Version') {
            steps {
                script {
                    echo "Using version: ${env.APP_VERSION}"
                    def imageName = "myapp:${env.APP_VERSION}"
                    echo "Docker image would be: ${imageName}"
                }
            }
        }

        stage('Jenkins Info') {
            steps {
                script {
                    echo "BUILD_NUMBER: ${env.BUILD_NUMBER}"
                    echo "BUILD_ID: ${env.BUILD_ID}"
                    echo "JOB_NAME: ${env.JOB_NAME}"
                    echo "WORKSPACE: ${env.WORKSPACE}"
                    echo "BUILD_URL: ${env.BUILD_URL}"
                }
            }
        }
    }
}