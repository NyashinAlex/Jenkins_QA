pipeline {
    agent any

    options {
        skipDefaultCheckout()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    parameters {
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'App version')
        choice(name: 'BUILD_TYPE', choices: ['clean', 'incremental'], description: 'Build type')
        booleanParam(name: 'RUN_PARALLEL_TESTS', defaultValue: true, description: 'Run tests in parallel')
    }

    environment {
        BUILD_VERSION = "${params.APP_VERSION}-${BUILD_NUMBER}"
        ENVIRONMENT = 'production'
    }

    stages {
        stage('Workspace Preparatione') {
            steps {
                script {
                    echo 'Preparing workspace...'
                    sh 'du -sh . 2>/dev/null || echo "Empty workspace"'
                    if(params.BUILD_TYPE == 'clean') {
                        deleteDir()
                    } else {
                        sh 'rm -rf python-app/dist/ python-app/build/ python-app/__pycache__/'
                    }
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
                echo 'Code checked out successfully'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('python-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    echo 'Dependencies installed'
                }
            }
        }

        stage('Build Application') {
            steps {
                dir('python-app') {
                    echo "Building version ${env.BUILD_VERSION}"
                    sh "APP_VERSION=${env.APP_VERSION} BUILD_NUMBER=${env.BUILD_NUMBER} python3 build.py"
                    sh 'ls -la dist/'
                }
            }
        }

        stage('Create Stashes') {
            steps {
                stash name: 'application-package', includes: 'python-app/dist/package/**'
                stash name: 'test-files', includes: 'python-app/test_app.py, python-app/app.py, python-app/requirements.txt'
                stash name: 'documentation', includes: 'python-app/dist/docs/**, python-app/dist/*.md'

                echo 'Created stashes for downstream stages'
            }
        }

        stage('Parallel Testing') {
            when {
                expression { params.RUN_PARALLEL_TESTS }
            }
            parallel {
                stage('Unit Tests') {
                    steps {
                        unstash 'test-files'
                        dir('python-app') {
                            sh "ENVIRONMENT=test APP_VERSION=${env.BUILD_VERSION} pytest -v --junit-xml=unit-test-results.xml"
                            stash name: 'unit-test-results', includes: 'python-app/unit-test-results.xml'
                        }
                    }
                }

                stage('Coverage Tests') {
                    steps {
                        unstash 'test-files'
                        dir('python-app') {
                            sh 'pytest --cov=app --cov-report=html --cov-report=xml'
                            stash name: 'coverage-reports', includes: 'python-app/htmlcov/**, python-app/coverage.xml'
                        }
                    }
                }

                stage('Package Validation') {
                    steps {
                        unstash 'application-package'
                        dir('python-app') {
                            sh 'pytest --cov=app --cov-report=html --cov-report=xml'
                            stash name: 'coverage-reports', includes: 'python-app/htmlcov/**, python-app/coverage.xml'
                            sh 'unzip -t application-package.zip'
                        }
                    }
                }
            }
        }

        stage('Collect Test Results') {
            steps {
                unstash 'unit-test-results'
                unstash 'coverage-reports'
                echo 'Test results collected'
            }
        }

        stage('Generate Reports') {
            steps {
                sh """
                === Build Summary ===
                Version: ${env.BUILD_VERSION}
                Build Number: ${env.BUILD_NUMBER}
                """
            }
        }

        stage('Archive All Artifacts') {
            steps {
                unstash 'application-package'
                unstash 'documentation'
                archiveArtifacts artifacts: 'python-app/dist/package/**', fingerprint: true
                archiveArtifacts artifacts: 'python-app/dist/docs/**', fingerprint: true
                archiveArtifacts artifacts: 'python-app/dist/*.json, python-app/dist/*.txt', fingerprint: true
                archiveArtifacts artifacts: 'python-app/*-test-results.xml, python-app/coverage.xml'
                archiveArtifacts artifacts: 'python-app/htmlcov/**'
                archiveArtifacts artifacts: 'final-report.txt'
            }
        }

        stage('Deploy to Staging') {
            when {
                anyOf {
                    branch 'master'
                    branch 'main'
                }
            }
            steps {
                unstash 'application-package'
                echo "Deploying version ${env.BUILD_VERSION} to staging..."
                sh 'ls -la python-app/dist/package/'
                sh 'sleep 2'
                echo 'Deployment to staging completed'
            }
        }
    }

    post {
        always {
            echo '=== Pipeline Execution Complete ==='
            echo "Duration: ${currentBuild.durationString}"
            sh 'du -sh . || echo "N/A"'
        }
        success {
            echo "✓ Build SUCCESS for version ${env.BUILD_VERSION}"
            archiveArtifacts artifacts: 'package', fingerprint: true
            cleanWs(
                patterns: [
                    [pattern: 'python-app/dist/package/**, .git/**', type: 'EXCLUDE'],
                    [pattern: 'python-app/dist/compiled/**, python-app/__pycache__/**, python-app/*.pyc', type: 'INCLUDE']
                ],
                deleteDirs: true
            )
            echo 'Workspace cleaned (temporary files removed, package preserved'
        }
        failure {
            echo """
            ✗ Build FAILED
            Build Number: ${env.BUILD_NUMBER}
            """
            archiveArtifacts artifacts: 'python-app/**/*.log, python-app/**/*.pyc', allowEmptyArchive: true
        }
        cleanup {
            sh 'rm -rf tmp/ .cache/ .pytest_cache/ || true'
            echo 'Cleanup completed'
        }
    }
}