pipeline {
    agent any

    environment {
        PROJECT_NAME = 'CloudStore'
        DEPLOY_ENVIRONMENT = 'staging'
        RUN_SECURITY_SCAN = 'true'
    }

    stages {
        stage('Initialization') {
            steps {
                script {
                    echo "Starting ${env.PROJECT_NAME} Pipeline"
                    def service = ['auth-service', 'api-gateway', 'user-service', 'payment-service']
                    env.SERVICES = service.join(',')
                    echo "Services to build: ${env.SERVICES}"
                }
            }
        }

        stage('Build Services') {
            steps {
                script {
                    def service = env.SERVICES.split(',')
                    service.each { server ->
                        echo "Building ${server}..."
                        sh "mkdir -p build/${server}"
                        sh "touch build/${server}/app.jar"
                        sh 'sleep 1'
                        echo "Build completed ${server}"
                    }
                }
            }
        }

        stage('Unit Tests') {
            steps {
                script {
                    if (env.BUILD_NUMBER.toInteger() % 2 == 0) {
                        echo "Running unit tests for build ${env.BUILD_NUMBER}"
                        sh 'sleep 2'
                        echo 'Unit tests passed'
                    }
                }
            }
        }

        stage('Integration Tests') {
            steps{
                script {
                    if (env.BUILD_NUMBER.toInteger() % 2 == 1) {
                        echo "Running integration tests for build ${env.BUILD_NUMBER}"
                        sh 'sleep 2'
                        echo 'Integration tests passed'
                    }
                }
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    if (env.RUN_SECURITY_SCAN == true) {
                        echo "Running security vulnerability scan..."
                        sh 'sleep 3'
                        echo 'Security scan completed - no vulnerabilities found'
                    }
                }
            }

            post {
                always {
                    echo 'Security scan stage finished'
                }
            }
        }

        stage('Deployment Approval') {
            steps {
                script {
                    if (env.DEPLOY_ENVIRONMENT == 'production' || env.DEPLOY_ENVIRONMENT == 'staging') {
                        timeout(time: 5, unit: 'MINUTES') {
                            def strategy = input (
                                message: "Approve deployment to ${DEPLOY_ENVIRONMENT}?",
                                parameters: [
                                    choice(
                                        name: 'DEPLOY_STRATEGY',
                                        choices: ['rolling', 'blue-green', 'canary']
                                    ),
                                    booleanParam(
                                        name: 'SEND_NOTIFICATIONS',
                                        defaultValue: true
                                    )
                                ]
                            )

                            env.DEPLOY_STRATEGY = strategy.DEPLOY_STRATEGY
                        }
                    }
                }
            }
        }

        stage('Deploy Services') {
            steps {
                script {
                    def services = [
                        staging: ['stage1.example.com', 'stage2.example.com'],
                        production: ['prod1.example.com', 'prod2.example.com', 'prod3.example.com']
                    ]

                    services.each {serviceName, productionName ->
                        echo "Deploying ${serviceName} to ${productionName} using ${env.DEPLOY_STRATEGY} strategy"
                        sh 'sleep 1'
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                echo """
                    === Pipeline Execution Complete ===
                    Total build time: ${new Date()}
                """
            }
        }
        success {
            script {
                def deploy =
                """
                    ✓ Deployment SUCCESS
                    Project: ${env.PROJECT_NAME}
                    Environment: ${env.DEPLOY_ENVIRONMENT}
                    All services deployed successfully
                """
                sh "echo '${deploy}' > deployment-report.txt"
            }
        }
        failure {
            script {
                def deploy =
                """
                    ✗ Deployment FAILED
                    Build Number: ${env.BUILD_NUMBER}
                    Check logs at: ${env.BUILD_URL}
                    Rolling back changes...
                """
                sh "echo '${deploy}' > deployment-report.txt"
            }
        }
        cleanup {
            echo """
                Cleaning up temporary files...
                Cleanup completed
            """
        }
    }
}