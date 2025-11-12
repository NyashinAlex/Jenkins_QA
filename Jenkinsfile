pipeline {
    agent any

    options {
        skipDefaultCheckout()
    }

    stages {
        stage('Inspect Workspace') {
            steps {
                dir('pwd') {
                    sh 'ls -la'
                    sh 'du -sh .'
                    echo "Workspace: ${env.WORKSPACE}"
                }
            }
        }

        stage('Create Test Files') {
            steps {
                sh 'echo "test" > test1.txt'
                sh 'mkdir -p temp && echo "temp" > temp/temp.log'
                sh 'mkdir -p old-build && echo "old" > old-build/app.jar'
                sh 'ls -R'
            }
        }

        stage('Selective Clean') {
            steps {
                sh 'rm -rf temp/ old-build/ *.log'
                echo 'Cleaned temporary files and old builds'
                sh 'ls -la'
            }
        }

        stage('Build with Selective Clean') {
            steps {
                sh 'rm -rf python-app/dist/ python-app/build/'
                sh 'mkdir -p python-app/dist'
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    sh "python3 build.py"
                }
            }
        }

        stage('Full Clean') {
            steps {
                deleteDir()
                echo 'Workspace cleaned completely'
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
                echo 'Code checked out'
            }
        }

        stage('Build from Clean State') {
            steps {
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    sh "python3 build.py"
                }
            }
        }

        stage('Build and Test') {
            steps {
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    sh "python3 build.py"
                    archiveArtifacts artifacts: 'python-app/dist/**'
                }
            }
        }

        stage('Smart Clean') {
            steps {
                sh 'rm -rf python-app/dist/ python-app/build/'
                sh 'if [ ! -d "python-app/venv" ]; then python3 -m venv python-app/venv; fi'
                sh 'Smart cleanup completed - dependencies preserved'
            }
        }

        stage('Build with Preserved Dependencies') {
            steps {
                sh '''
                    source python-app/venv/bin/activate
                    python --version
                    pip install -r requirements.txt
                '''
            }

            post {
                always {
                    sh 'du -sh .'
                    sh 'du -ah . | sort -rh | head -10'
                }
            }
        }
    }

    post {
        always {
            sh 'du -sh .'
            cleanWs() {
                deleteDirs: true,
                patterns: [
                    [pattern: 'python-app/dist/compiled/**', type: INCLUDE],
                    [pattern: 'python-app/__pycache__/**', type: INCLUDE],
                    [pattern: 'python-app/*.pyc', type: INCLUDE],
                    [pattern: 'python-app/dist/package/**', type: EXCLUDE],
                    [pattern: '.git/**', type: EXCLUDE]
                ]
            }
            echo 'Workspace cleaned after build'
        }
        success {
            sh 'rm -rf python-app/dist/compiled/ python-app/__pycache__/'
            echo 'Cleaned temporary files after successful build'
        }
        failure {
            archiveArtifacts artifacts: 'python-app/**/*.log', allowEmptyArchive: true
            echo 'Workspace preserved for debugging'
        }
        cleanup {
            sh 'rm -rf .cache/ tmp/'
        }
    }
}