pipeline {
    agent any

    parameters {
        string(name: 'REPO_URL', defaultValue: '', description: 'Repository URL to set in YAML files')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        durabilityHint('MAX_SURVIVABILITY')
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/venkatesh-reddy-prog/Template_Repo.git', credentialsId: 'WAS'
            }
        }

        stage('Modify YAML Files') {
            steps {
                script {
                    def yamlFiles = [
                        'bic/applications/additional-secrets.yaml',
                        'bic/applications/btp-secrets.yaml',
                        'bic/applications/postgres-app.yaml'
                    ]

                    yamlFiles.each { yamlFile ->
                        if (fileExists(yamlFile)) {
                            def yamlContent = readYaml file: yamlFile
                            yamlContent.spec.sources.each { source ->
                                source.repoURL = params.REPO_URL
                            }
                            writeYaml file: yamlFile, data: yamlContent
                            echo "Modified file: ${yamlFile} with new repoURL: ${params.REPO_URL}"
                        } else {
                            error "File ${yamlFile} not found!"
                        }
                    }
                }
            }
        }

        stage('Commit and Push Changes') {
            steps {
                script {
                    def commitMessage = "Update repoURL to ${params.REPO_URL} in all YAML files"
                    bat 'git config --global user.email "bvenkateshreddy87@gmail.com"'
                    bat 'git config --global user.name "B Venkatesh Reddy"'
                    bat 'git add .'
                    bat "git commit -m \"${commitMessage}\""
                    bat 'git push origin main'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline successful'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            cleanWs()
        }
    }
}
