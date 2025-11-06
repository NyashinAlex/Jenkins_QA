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

        stage('Run Application with Secrets') {
            steps {
                withCredentials([
                    string(credentialsId: 'api-key', variable: 'API_KEY'),
                    string(credentialsId: 'database-url', variable: 'DATABASE_URL')
                ]) {
                    dir('app') {
                        echo 'Starting application with all credentials configured'
                        sh "NODE_ENV=${env.NODE_ENV} APP_VERSION=${env.APP_VERSION} BUILD_NUMBER=${env.BUILD_NUMBER} API_KEY=${env.API_KEY} DATABASE_URL=${env.DATABASE_URL} npm start &"
                        sh 'sleep 3'
                        RESPONSE = '$(curl -s http://localhost:3000/config)'
                        echo "${env.RESPONSE}" | grep -q '"apiKeyConfigured":true' || exit 1
                        echo "${env.RESPONSE}" | grep -q '"databaseConfigured":true' || exit 1
                        sh 'pkill -f "node server.js"'
                    }
                }
            }
        }

        stage('Production Deploy') {
            steps {
                when {
                    environment name: 'NODE_ENV', value: 'production'
                }

                withCredentials([
                    string(credentialsId: 'api-key', variable: 'API_KEY'),
                    string(credentialsId: 'database-url', variable: 'DATABASE_URL'),
                    usernamePassword(
                        credentialsId: 'database-creds',
                        usernameVariable: 'DB_USER',
                        passwordVariable: 'DB_PASS'
                    )]) {
                    echo 'Deploying to production with full configuration'
                    echo "API Key: ${env.API_KEY}"
                    echo "Database: ${env.DB_USER}@${env.DATABASE_URL}"
                    echo "Environment: ${env.NODE_ENV}"
                    echo "Deployment completed successfully"
                }
            }
        }
    }
}