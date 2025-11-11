pipeline {
    agent any

    environment {
        APP_VERSION = '1.0.0'
        ENVIRONMENT = 'production'
    }

    stages {
        stage('Build') {
            steps {
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    sh "APP_VERSION=${env.APP_VERSION} BUILD_NUMBER=${env.BUILD_NUMBER} python3 build.py"
                    stash name: 'built-app', includes: 'python-app/dist/**', excludes: 'python-app/dist/package/*.pyc'
                    stash name: 'package-only', includes: 'python-app/dist/**'
                }
            }
        }

        stage('Verify Build') {
            steps {
                unstash 'built-app'
                sh 'ls -la python-app/dist/'
                echo 'cat python-app/dist/build-info.json'
            }
        }

        stage('Package Test') {
            steps {
                unstash 'package-only'
                sh 'ls -la python-app/dist/package/'
                echo 'cat python-app/dist/build-info.json'
            }
        }
    }
}