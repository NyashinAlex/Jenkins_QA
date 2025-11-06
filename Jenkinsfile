pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    environment {
        APP_NAME = 'jenkins-sample-app'
        NODE_ENV = 'development'
        PORT = '3000'
        APP_VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Show Build Info') {
            steps {
                echo "Build Number: ${BUILD_NUMBER}"
                echo "Job Name: ${JOB_NAME}"
                echo "Workspace: ${WORKSPACE}"
                echo "Build URL: ${BUILD_URL}"
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('app'){
                    sh 'node -v'
                    sh 'npm install'
                }

                echo "Dependencies installed for ${env.APP_NAME}"
            }
        }

        stage('Build') {
            steps {
                dir('app') {
                    echo "Building App version: ${env.APP_VERSION}"
                    sh 'npm run build'
                    echo 'Build completed successfully'
                }
            }
        }

        stage('Test') {
//             environment {
//                 NODE_ENV = 'test'
//             }

            steps {
                dir('app') {
                    echo "Running tests in ${env.NODE_ENV} environment"
                    sh "NODE_ENV=${env.NODE_ENV} APP_VERSION=${env.APP_VERSION} npm test"
                }
            }
        }

        stage('Run Application') {
            steps {
                dir('app') {
                    echo "Starting App name ${env.APP_NAME} and port ${env.PORT}"
                    sh "NODE_ENV=${env.NODE_ENV} APP_VERSION=${env.APP_VERSION} BUILD_NUMBER=${env.BUILD_NUMBER} PORT=${env.PORT} npm start &"
                    sh 'sleep 3'
                    sh "curl http://localhost:${env.PORT}/"
                    sh "curl http://localhost:${env.PORT}/config"
                    sh 'pkill -f "node server.js"'
                }
            }
        }

        stage('Summary') {
            steps {
                script {
                    if(env.NODE_ENV == 'development') {
                        echo 'Running in development mode'
                    } else {
                        echo 'Running in production mode'
                    }

                    echo """
                        Application: ${env.APP_NAME}
                        Version: ${env.APP_VERSION}
                        Environment: ${env.NODE_ENV}
                        Build completed at build #${env.BUILD_NUMBER}
                    """
                }
            }
        }
    }
}