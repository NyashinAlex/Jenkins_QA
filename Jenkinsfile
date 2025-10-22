pipeline {
    agent any
    stages {
        stage('Check Agent') {
            steps {
                echo 'Running on agent...'
                sh 'hostname'
                echo "WORKSPACE: ${WORKSPACE}"
                echo "NODE NAME: ${NODE_NAME}"
            }
        }
    }
}