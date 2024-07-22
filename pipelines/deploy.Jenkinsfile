pipeline {
    agent any

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: 'Name of the service directory')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: 'Full name of the Docker image to use')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    dir("${params.SERVICE_NAME}") {
                        // Print the current directory and list its contents for debugging
                        sh 'pwd'
                        sh 'ls -la'

                        // Ensure deployment.yaml exists before attempting to modify it
                        sh '''
                        if [ -f deployment.yaml ]; then
                            sed -i "s|image: .*|image: ${IMAGE_FULL_NAME_PARAM}|" deployment.yaml
                            git add deployment.yaml
                            git commit -m "Jenkins deploy ${SERVICE_NAME} ${IMAGE_FULL_NAME_PARAM}"
                            git push origin main
                        else
                            echo "Error: deployment.yaml not found in $(pwd)"
                            exit 1
                        fi
                        '''
                    }
                }
            }
        }
    }
}
