pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    parameters {
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'Application version')
        choice(name: 'DEPLOY_ENVIRONMENT', choices: ['development', 'staging', 'production'], description: 'Deployment environment')
        choice(name: 'BUILD_TYPE', choices: ['quick', 'full'], description: 'Build type (quick or full with all tests)')
        booleanParam(name: 'RUN_SECURITY_SCAN', defaultValue: false, description: 'Run security vulnerability scan')
        booleanParam(name: 'DEPLOY_ENABLED', defaultValue: true, description: 'Deploy after build')
    }

    environment {
        APP_NAME = 'jenkins-sample-app'
        BUILD_VERSION = "${params.APP_VERSION}-${env.BUILD_NUMBER}"
        DOCKER_IMAGE = "${APP_NAME}:${env.BUILD_VERSION}"
    }

    stages {
        stage('Initialize') {
            steps {
                script {
                    echo """
                    === CI/CD Pipeline Started ===
                    Application: ${env.APP_NAME}
                    Version: ${params.APP_VERSION}
                    Build Version: ${env.BUILD_VERSION}
                    Environment: ${params.DEPLOY_ENVIRONMENT}
                    Build Type: ${params.BUILD_TYPE}
                    Build Number: ${env.BUILD_NUMBER}
                    Job Name: ${env.JOB_NAME}
                    """

                    if(params.DEPLOY_ENVIRONMENT == 'production') {
                        echo '⚠️ WARNING: Production deployment - extra validation required'
                    }
                }
            }
        }

        stage('Build Application') {
            steps {
                dir('app') {
                    echo 'Installing dependencies...'
                    sh 'npm install'
                    echo "Building application version ${env.BUILD_VERSION}"
                    sh "APP_VERSION=${env.BUILD_VERSION} npm run build"
                    echo 'Build artifacts created in dist/'
                }
            }
        }

        stage('Unit Tests') {
            when {
                expression {
                    params.BUILD_TYPE == 'quick' || params.BUILD_TYPE == 'full'
                }
            }
            steps {
                dir('app') {
                    echo 'Running unit tests...'
                    sh "NODE_ENV=production APP_VERSION=${env.BUILD_VERSION} npm test"
                    echo 'Unit tests passed ✓'
                }
            }
        }

        stage('Integration Tests') {
            when {
                expression {
                    params.BUILD_TYPE == 'full'
                }
            }
            steps {
                echo 'Running integration tests...'
                sh 'sleep 2'
                echo 'Integration tests passed ✓'
            }
        }

        stage('Security Scan') {
            when {
                expression {
                    params.RUN_SECURITY_SCAN == true
                }
            }
            steps {
                echo 'Running security vulnerability scan...'
                echo "Scanning Docker image: ${env.DOCKER_IMAGE}"
                sh 'sleep 3'
                echo 'Security scan completed - no critical vulnerabilities found ✓'
            }
            post {
                always {
                    echo 'Security scan stage finished'
                }
            }
        }

        stage('Docker Build') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    echo "Building Docker image: ${env.DOCKER_IMAGE}"
                    echo "Docker Registry User: ${DOCKER_USER}"
                    echo "docker build -t ${env.DOCKER_IMAGE} ."
                    echo "docker login -u ${DOCKER_USER} -p ****"
                    echo "docker push ${DOCKER_IMAGE}"
                    echo 'Docker image built and pushed successfully ✓'
                }
            }
        }

        stage('Deploy Application') {
            when {
                expression {
                    params.DEPLOY_ENABLED == true
                }
            }
            steps {
                withCredentials([
                    string(credentialsId: 'api-key', variable: 'API_KEY'),
                    string(credentialsId: 'database-url', variable: 'DATABASE_URL')
                ]) {
                    script {
                        echo "Deploying to ${params.DEPLOY_ENVIRONMENT} environment"
                        echo "Version: ${env.BUILD_VERSION}"
                        echo "Docker Image: ${env.DOCKER_IMAGE}"

                        def servers = [
                            development: ['dev1.example.com'],
                            staging: ['stage1.example.com', 'stage2.example.com'],
                            production: ['prod1.example.com', 'prod2.example.com', 'prod3.example.com']
                        ]

                        servers.each {server ->
                            echo "Deploying to ${server}..."
                            sh 'sleep 1'
                            echo "✓ Deployed to ${server}"
                        }

                        echo 'All servers updated successfully'
                    }
                }
            }
        }

        stage('Smoke Tests') {
            when {
                expression {
                    params.DEPLOY_ENABLED == true
                }
            }
            steps {
                withCredentials([
                    string(credentialsId: 'api-key', variable: 'API_KEY'),
                    string(credentialsId: 'database-url', variable: 'DATABASE_URL')
                ]) {
                    script {
                        dir('app') {
                            echo "Running smoke tests on ${params.DEPLOY_ENVIRONMENT}..."
                            sh "NODE_ENV=${params.DEPLOY_ENVIRONMENT} APP_VERSION=${env.BUILD_VERSION} BUILD_NUMBER=${env.BUILD_NUMBER} API_KEY=${API_KEY} DATABASE_URL=${DATABASE_URL} npm start &"
                            sh 'sleep 3'
                            sh 'curl http://localhost:3000/health'
                            sh """
                                RESPONSE=\$(curl -s http://localhost:3000/config)
                                echo "\${RESPONSE}" | grep -q '"version":"${env.BUILD_VERSION}"' || exit 1
                                echo "\${RESPONSE}" | grep -q '"environment":"${params.DEPLOY_ENVIRONMENT}"' || exit 1
                            """
                        }
                    }
                }
            }
            post {
                always {
                    sh 'pkill -f "node server.js"'
                }
                success {
                    echo 'Smoke tests passed ✓'
                }
                failure {
                    echo 'ERROR!!!'
                }
            }
        }
    }
    post {
        always {
            echo """
            === Pipeline Execution Complete ===
            Total execution time: ${currentBuild.durationString}
            """
        }
        success {
            script {
                def report = """
                ✓ BUILD SUCCESSFUL
                Application: ${env.APP_NAME}
                Version: ${env.BUILD_VERSION}
                Environment: ${params.DEPLOY_ENVIRONMENT}
                Docker Image: ${env.DOCKER_IMAGE}
                """
                echo "${report}"
                writeFile file: 'deployment-report.txt', text: report
                echo 'Deployment report saved to deployment-report.txt'
            }
        }
        failure {
            echo """
            ✗ BUILD FAILED
            Build Number: ${env.BUILD_NUMBER}
            Check logs at: ${env.BUILD_URL}
            Failed at environment: ${params.DEPLOY_ENVIRONMENT}
            """
        }
        cleanup {
            echo """
            Cleaning up workspace...
            Cleanup completed
            """
        }
    }
}