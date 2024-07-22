pipeline {
    agent any

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: '')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: '')
    }

    stages {
        stage('Deploy') {
            steps {
                sh '''
                cd $SERVICE_NAME
                sed -i "s|image: .*|image: ${IMAGE_FULL_NAME_PARAM}|" deployment.yaml
                git add deployment.yaml
                git commit -m "Jenkins deploy $SERVICE_NAME $IMAGE_FULL_NAME_PARAM"
                git push origin main
                '''
            }
        }
    }
}