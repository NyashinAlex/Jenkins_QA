pipeline {
    agent any

    environment {
        APP_NAME = 'jenkins-sample-app'
        NODE_ENV = 'production'
        APP_VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                dir('app') {
                    sh 'npm install'
                    sh 'npm run build'
                    echo "Build completed for version ${env.APP_VERSION}"
                }
            }
        }

        stage('Test with API Key') {
            steps {
                withCredentials([string(credentialsId: 'api-key', variable: 'API_KEY')]) {
                    dir('app') {
                        sh "API_KEY=${env.API_KEY} NODE_ENV=test APP_VERSION=${env.APP_VERSION} npm test"
                    }
                    echo 'Tests completed with API key configured'
                    echo "API_KEY: ${env.API_KEY}"
                }
            }
        }
    }
}