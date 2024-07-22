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

                        // Update the correct YAML file
                        def yamlFile = 'frontend.yaml'
                        if (fileExists(yamlFile)) {
                            sh """
                            sed -i "s|image: .*|image: ${params.IMAGE_FULL_NAME_PARAM}|" ${yamlFile}
                            git add ${yamlFile}
                            git commit -m "Jenkins deploy ${params.SERVICE_NAME} ${params.IMAGE_FULL_NAME_PARAM}"
                            git push origin main
                            """
                        } else {
                            error "Error: ${yamlFile} not found in $(pwd)"
                        }
                    }
                }
            }
        }
    }
}
