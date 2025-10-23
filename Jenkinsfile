pipeline {
    agent any

    stages {
        stage('List Basics') {
            steps {
                script {
                    dev environments = ['dev', 'staging', 'production']
                    echo "First element by environments: ${environments.get[0]}"
                    echo "Last element by environments: ${environments.get[-1]}"
                    echo "Size environments: ${environments.size()}"

                    environments.add(qa)
                    echo "Mew size environments: ${environments.size()}"
                }
            }
        }

        stage('Deploy to Servers') {
            steps {
                script {
                    def servers =  ['server1.example.com', 'server2.example.com', 'server3.example.com']
                    for (server in servers) {
                        echo "Deploying to ${server}"
                        sh 'sleep 1'
                        echo "Deployment to ${server} completed"
                    }
                }
            }
        }

        stage('Configuration Map') {
            steps {
                script {
                    def configuration = [
                        'appName', 'MyWebApp'
                        'version', '2.0.0'
                        'port', 8080
                        'environment', 'production'
                    ]

                    echo "appName: ${appName}"
                    echo "version: ${version}"
                    echo "port: ${port}"
                    echo "environment: ${environment}"
                    echo "Count elements in configuration: ${configuration.size()}"

                    configuration.add('region', 'us-east-1')
                    echo "configuration: ${configuration}"
                }
            }
        }
    }
}