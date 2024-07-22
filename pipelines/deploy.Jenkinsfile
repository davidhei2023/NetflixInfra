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
                sh '''
                FILE="NetflixInfra/k8s/NetflixMovieCatalog/deployment.yaml"
                if [ -f "$FILE" ]; then
                    sed -i "s|image: .*|image: ${IMAGE_FULL_NAME_PARAM}|" $FILE
                    git add $FILE
                    git commit -m "Jenkins deploy ${SERVICE_NAME} ${IMAGE_FULL_NAME_PARAM}"
                else
                    echo "Error: $FILE not found"
                    exit 1
                fi
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
