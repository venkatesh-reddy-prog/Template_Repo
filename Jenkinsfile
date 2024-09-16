pipeline {
    agent any
    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://example.com/repo.git', description: 'Repository URL')
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: 'main']],
                          userRemoteConfigs: [[url: 'https://github.com/venkatesh-reddy-prog/Template_Repo.git']]
                ])
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
                            try {
                                def yamlContent = readYaml file: yamlFile
                                echo "Original content of ${yamlFile}: ${yamlContent}"

                                // Modify YAML content based on structure
                                if (yamlContent.spec?.source) {
                                    yamlContent.spec.source.repoURL = params.REPO_URL
                                } else if (yamlContent.spec?.sources) {
                                    yamlContent.spec.sources.each { source ->
                                        source.repoURL = params.REPO_URL
                                    }
                                } else {
                                    error "Unrecognized structure in ${yamlFile}"
                                }

                                writeYaml file: yamlFile, data: yamlContent
                                echo "Modified file: ${yamlFile} with new repoURL: ${params.REPO_URL}"
                            } catch (Exception e) {
                                error "Failed to process ${yamlFile}: ${e.message}"
                            }
                        } else {
                            error "File ${yamlFile} not found!"
                        }
                    }
                }
            }
        }

        stage('Commit and Push Changes') {
            when {
                expression { return fileExists('bic/applications/additional-secrets.yaml') || fileExists('bic/applications/btp-secrets.yaml') || fileExists('bic/applications/postgres-app.yaml') }
            }
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'your-credentials-id', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh '''
                        git config user.name "Jenkins"
                        git config user.email "jenkins@example.com"
                        git add .
                        git commit -m "Updated YAML files with new repo URL"
                        git push origin main
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
