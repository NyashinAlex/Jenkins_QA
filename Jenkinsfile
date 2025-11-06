pipeline {
    agent any

    environment {
        PROJECT_NAME = 'CloudStore'
        DEPLOY_ENVIRONMENT = 'production'
        RUN_SECURITY_SCAN = 'false'
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
    }
}