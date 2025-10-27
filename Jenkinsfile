pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mkdir build'
                sh 'echo "Application binary" > build/app.jar'
            }

            post {
                always {
                    echo '=== Post Actions ==='
                    echo 'Pipeline completed'
                    sh 'date'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'sleep 2'
                echo 'Tests completed'
            }

            post {
                success {
                    echo '✓ Build SUCCESS'
                    echo "Build Number: ${env.BUILD_NUMBER}"
                    echo 'All stages passed successfully'
                }
                failure {
                    echo '✗ Build FAILED'
                    echo "Build Number: ${env.BUILD_NUMBER}"
                    echo 'Check console output for details'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh 'sleep 3'
                echo 'Deployment completed'
            }
        }

        post {
            success {
                echo 'Deploy stage finished'
                sh 'ls -la build/'

                echo 'Archiving build artifacts...'
                sh 'tar -czf build.tar.gz build/'
                sh 'ls -lh build.tar.gz'
                echo 'Artifacts archived successfully'
            }
            cleanup {
                echo '=== Cleanup Phase ==='
                echo 'Removing temporary files...'
                sh 'mkdir temp && rm -rf temp'
                echo 'Cleanup completed'
            }
        }
    }
}