pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'sleep 2'
                echo 'Build completed'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'sleep 2'
                echo 'Tests passed'
            }
        }

        stage('Deploy to Production') {
            steps {
                input message: 'Deploy to production?'

                sh 'sleep 3'
                echo 'Deployment completed successfully'
            }
        }

        stage('Notify Team') {
            steps {
                input message: 'Send notification to the team?'
                           ok: 'Send Notification'

                echo 'Sending notification...'
                echo 'Notification sent to team@company.com'
            }
        }

        stage('Deploy Strategy') {
            steps {
                script {
                    def strategy = input(
                        message: 'Select deployment strategy'
                        parameters: [
                            choice(
                                name: 'STRATEGY',
                                choice: ['rolling', 'blue-green', 'canary']
                            )
                        ]
                    )

                    echo "Selected strategy: ${strategy}"
                    if(${strategy} == 'rolling') {
                        echo 'Deploying with rolling update...'
                    }
                    if(${strategy} == 'blue-green') {
                        echo 'Deploying with blue-green update...'
                    }
                    if(${strategy} == 'canary') {
                        echo 'Deploying with canary update...'
                    }
                }
            }
        }

        stage('Approval with Timeout') {
            steps {
                timeout:(time: 2, unit: 'MINUTES') {
                    input message: 'Approve within 2 minutes'

                    echo 'Approval received in time'
                }
            }
        }

        stage('Advanced Approval') {
            steps {
                script {
                    echo 'Configure deployment'
                    def params = input(
                        parameters: [
                            string(
                                name: 'VERSION'
                                defaultValue: '1.0.0'
                            ),
                            choice(
                                name: 'ENVIRONMENT'
                                choice: ['staging', 'production']
                            ),
                            booleanParam(
                                name: 'SEND_NOTIFICATION'
                                defaultValue: true
                            )
                        ]
                    )

                    echo "VERSION: ${parameters.VERSION}"
                    echo "ENVIRONMENT: ${parameters.ENVIRONMENT}"
                    echo "SEND_NOTIFICATION: ${parameters.SEND_NOTIFICATION}"
                }
            }
        }
    }
}