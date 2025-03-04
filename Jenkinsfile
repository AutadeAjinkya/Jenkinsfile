pipeline {
    agent any

    environment {
        KUBE_NAMESPACE = "gamma"
        IMAGE_NAME = "ajinkyaautade09/my-java-app"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS_ID = "docker-hub"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AutadeAjinkya/Jenkinsfile.git'
            }
        }

        stage('Build') {
            steps {
                sh '''
                apt-get update && apt-get install -y maven
                mvn clean package
                '''
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
                        sh "docker push $IMAGE_NAME:$IMAGE_TAG"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl create namespace $KUBE_NAMESPACE || true
                helm upgrade --install my-java-app ./my-java-app-chart -n $KUBE_NAMESPACE \
                  --set image.repository=$IMAGE_NAME --set image.tag=$IMAGE_TAG
                """
            }
        }
    }
}
