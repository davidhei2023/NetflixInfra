pipeline {
    agent {
        label 'general'
    }

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: '')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: '')
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Git setup') {
            steps {
                sh '''
                git checkout main
                git pull origin main --rebase  // Rebase to reconcile divergent branches
                '''
            }
        }
        stage('Update YAML manifests') {
            steps {
                script {
                    def yamlFilePath = "${params.SERVICE_NAME}/frontend.yaml"
                    sh """
                    if [ -f ${yamlFilePath} ]; then
                        sed -i 's|image: .*|image: ${params.IMAGE_FULL_NAME_PARAM}|' ${yamlFilePath}
                        git add ${yamlFilePath}
                        git commit -m 'Jenkins deploy ${params.SERVICE_NAME} ${params.IMAGE_FULL_NAME_PARAM}'
                    else
                        echo 'Error: ${yamlFilePath} not found'
                        exit 1
                    fi
                    """
                }
            }
        }
        stage('Git push') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                 sh '''
                 git push https://$GITHUB_TOKEN@github.com/davidhei2023/NetflixInfra.git main
                 '''
               }
            }
        }
    }
}
