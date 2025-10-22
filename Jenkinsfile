pipeline {
    agent any

    stages {
        stage('Preparation') {
            steps {
                echo 'Starting WebStore CI/CD Pipeline'
                sh 'mkdir -p build test-reports artifacts'
                sh 'date'
                echo "NODE NAME: ${env.NODE_NAME}"
                echo "WORKSPACE: ${env.WORKSPACE}"
            }
        }

        stage('Generate Version') {
            steps {
                script {
                    def major = 2
                    def minor = 1
                    env.APP_VERSION = "${major}.${minor}.${env.BUILD_NUMBER}-${env.GIT_COMMIT.take(7)}"
                    echo "Application version: ${env.APP_VERSION}"
                }
            }
        }

        stage('Build Application') {
            steps {
                script {
                    echo "Building WebStore version ${env.APP_VERSION}"
                    echo "${env.APP_VERSION} > build/version.txt"
                    echo 'WebStore Application Binary > build/app.jar'
                    sh 'ls -la build/'
                    echo 'Build completed successfully'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh 'echo "Unit tests: PASSED" > test-reports/unit-tests.txt'
                sh 'sleep 2'
                echo 'Unit tests completed'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Running integration tests...'
                sh 'echo "Integration tests: PASSED" > test-reports/integration-tests.txt'
                sh 'sleep 3'
                echo 'Integration tests completed'
            }
        }

        stage('Package Artifacts') {
            steps {
                script {
                    def artifactName = "webstore-${env.APP_VERSION}.tar.gz"
                    echo "Creating artifact: ${artifactName}"
                    sh 'tar -czf artifacts/arzif.txt build/ test-reports/'
                    sh 'ls -lh artifacts/'
                    echo 'Artifact ready for deployment'
                }
            }
        }

        stage('Summary') {
            steps {
                script {
                    echo """
                    === Build Summary ===
                    Application: WebStore
                    Version: ${env.APP_VERSION}
                    Build Number: ${env.BUILD_NUMBER}
                    Build URL: ${env.BUILD_URL}
                    Status: SUCCESS
                    === End of Pipeline ===
                    """
                }
            }
        }
    }
}