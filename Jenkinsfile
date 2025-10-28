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
    }
}