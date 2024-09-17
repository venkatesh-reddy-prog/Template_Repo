pipeline {
    agent any

    parameters {
        string(name: 'NEW_REPO_URL')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: 'https://github.com/venkatesh-reddy-prog/Template_Repo.git']]
                ])
            }
        }
        
        stage('Run Script') {
            steps {
                script {
                    withEnv(["NEW_REPO_URL=${params.NEW_REPO_URL}"]) {
                        bat 'python templatee.py'
                    }
                }
            }
        }
    }
}
