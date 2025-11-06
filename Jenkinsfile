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

        stage('Configure Database') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'database-creds',
                    usernameVariable: 'DB_USER',
                    passwordVariable: 'DB_PASS'
                    )]) {
                    echo 'Configuring database connection...'
                    echo "Database user: ${env.DB_USER}"
                    echo "Database password: ${env.DB_PASS}"
                    sh "cat 'echo "DB_USER=${env.DB_USER}" > app/db.config'"
                    sh "cat 'echo "DB_PASS=${env.DB_PASS}" >> app/db.config"
                    echo 'Database configuration created'
                }
            }
        }
    }
}