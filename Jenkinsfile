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
    }
}