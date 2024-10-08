pipeline {
    agent {
        label 'general'
    }

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: 'Service name to deploy')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: 'Full name of the Docker image')
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()  // Clean the workspace before starting the build
            }
        }
        stage('Checkout SCM') {
            steps {
                checkout scm  // Check out the source code
            }
        }
        stage('Git setup') {
            steps {
                sh '''
                git checkout dev
                git pull origin main --rebase
                '''
            }
        }
        stage('Update YAML manifests') {
            steps {
                script {
                    def yamlFilePath = "k8s/NetflixMovieCatalog/deployment.yaml"  // Correct path to the YAML file
                    sh """
                        sed -i 's|image: .*|image: ${params.IMAGE_FULL_NAME_PARAM}|' ${yamlFilePath}
                        git add ${yamlFilePath}
                        git commit -m 'Jenkins deploy ${params.SERVICE_NAME} ${params.IMAGE_FULL_NAME_PARAM}'
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