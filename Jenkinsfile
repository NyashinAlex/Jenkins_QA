pipeline {
    agent none
    stages {
        stage('Check Agent') {
            agent any

            steps {
                echo 'Running on agent...'
                sh 'hostname'
                echo "WORKSPACE: ${WORKSPACE}"
                echo "NODE NAME: ${NODE_NAME}"
            }
        }

        stage('Build Info') {
            agent any

            steps {
                echo 'Build information...'
                echo "BUILD NUMBER: ${BUILD_NUMBER}"
                echo "BUILD ID: ${BUILD_ID}"
                echo "BUILD URL: ${BUILD_URL}"
            }
        }
    }
}