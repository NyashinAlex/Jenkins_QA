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
    }
}