pipeline {
    agent any

    environment {
        GIT_SHORT_COMMIT = env.GIT_COMMIT.take(7)
        BUILD_VERSION = "1.0.${BUILD_NUMBER}-${GIT_SHORT_COMMIT}"
    }

    stages {
        stage('Git Info') {
            steps {
                echo "GIT_BRANCH: ${env.GIT_BRANCH}"
                echo "GIT_COMMIT: ${env.GIT_COMMIT}"
                echo "GIT_SHORT_COMMIT: ${env.GIT_SHORT_COMMIT}"
                echo "GIT_AUTHOR_NAME: ${env.GIT_AUTHOR_NAME}"
                echo "GIT_AUTHOR_EMAIL: ${env.GIT_AUTHOR_EMAIL}"
                echo "GIT_URL: ${env.GIT_URL}"
                echo "BUILD_VERSION: ${env.BUILD_VERSION}"
            }
        }

        stage('Commit Message') {
            steps {
                script {
                    def commitMsg = sh(
                        script: 'git log -1 --pretty=%B',
                        returnStdout: true
                    ).trim()

                    echo "Commit message: ${commitMsg}"

                    if (commitMsg.contains('[skip ci]')) {
                        echo 'ERROR - msg [skip ci]'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                dir('flask-app') {
                    sh 'pip3 install -r requirements.txt --break-system-packages'
                    echo "BUILD_VERSION: ${env.BUILD_VERSION}"
                }
            }
        }

        stage('Create Git Tag') {
            when {
                branch 'main'
            }

            steps {
                sh 'git config user.name "Jenkins CI"'
                sh 'git config user.email "jenkins@company.com"'
                sh "git tag -a ${env.BUILD_VERSION} -m \"Release ${version}\""
            }
        }

        stage('Git Stats') {
            steps {
                sh 'git rev-list --count HEAD'
                sh 'git log -5 --pretty=format:"%h - %an: %s"'
                sh "git diff-tree --no-commit-id --name-only -r HEAD"
            }
        }
    }
}