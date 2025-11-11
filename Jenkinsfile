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

        stage('Build and Stash Multiple') {
            steps {
                stash name: 'binaries', includes: 'python-app/dist/package/**'
                stash name: 'docs', includes: 'python-app/dist/docs/**'
                stash name: 'metadata', includes: 'python-app/dist/*.json, python-app/dist/*.txt'
            }
        }

        stage('Use Binaries') {
            steps {
                unstash 'binaries'
                sh 'ls -la python-app/dist/package/'
                echo 'cat python-app/dist/package/VERSION'
            }
        }

        stage('Publish Docs') {
            steps {
                unstash 'docs'
                echo 'cat python-app/dist/docs/API.md'
            }
        }

        stage('Check Metadata') {
            steps {
                unstash 'metadata'
                echo 'cat python-app/dist/build-info.json && cat python-app/dist/BUILD-REPORT.txt'
            }
        }

        stage('Build for Tests') {
            steps {
                stages name: 'app-for-testing', includes: 'python-app/dist/package/**'
            }
        }

        stage('Parallel Tests') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        unstash 'app-for-testing'
                        dir('python-app') {
                            sh "ENVIRONMENT=test APP_VERSION=${env.APP_VERSION} python3 -m pytest -v test_app.py"
                        }
                    }
                }

                stage('Integration Tests') {
                    steps {
                        unstash 'app-for-testing'
                        echo 'Running integration tests...'
                        sh 'sleep 2'
                        echo 'Integration tests passed'
                    }
                }

                stage('Package Validation') {
                    steps {
                        unstash 'app-for-testing'
                        ls 'ls -la python-app/dist/package/'
                    }
                }
            }
        }
    }
}