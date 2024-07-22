pipeline {
    agent {
        label 'general'
    }

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: 'Service name to deploy')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: 'Full name of the Docker image')
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Git setup') {
            steps {
                sh '''
                git checkout main
                git pull origin main
                '''
            }
        }
        stage('Update YAML manifests') {
            steps {
                sh '''
                DEPLOYMENT="NetflixInfra/k8s/NetflixMovieCatalog/deployment.yaml"
                    sed -i "s|image: .*|image: ${IMAGE_FULL_NAME_PARAM}|" $DEPLOYMENT
                    git add $FILE
                    git commit -m "Jenkins deploy ${SERVICE_NAME} ${IMAGE_FULL_NAME_PARAM}"
                '''
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
