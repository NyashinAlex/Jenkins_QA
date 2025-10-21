pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                echo 'Preparing workspace...'
                sh 'mkdir -p build logs temp'
                echo 'Directories created'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'echo "Build version: 1.0.0" > build/version.txt'
                echo 'Build completed'
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying build...'
                sh 'cat build/version.txt'
                sh 'ls -la build/'
                echo 'Verification completed'
            }
        }

        stage('System Info') {
            steps {
                echo '=== System Information ==='
                echo 'User'
                sh 'whoami'
                echo 'Info about disk'
                sh 'df -h .'
                echo "Build Number: ${BUILD_NUMBER}"
                echo "Job Name: ${JOB_NAME}"
            }
        }
    }
}