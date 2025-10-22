pipeline {
    agent any

    stages {
        stage('Variables Demo') {
            steps {
                script {
                    def appName = 'MyApplication'
                    def port = 8080
                    def isProduction = false
                    echo "App name, port and is_production: ${appName}, ${port}, ${isProduction}"
                }
            }
        }

        stage('String Operations') {
            steps {
                script {
                    def message = 'Jenkins Pipeline Tutorial'
                    echo "Size message: ${message.length()}"
                    echo "Upper case message: ${message.toUpperCase()}"
                    echo "Lower case message: ${message.toLowerCase()}"
                    echo "New message: ${message.replace('Tutorial', 'Course')}"
                }
            }
        }
    }
}