pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/label: jenkins-maven-agent
spec:
  containers:
  - name: maven
    image: ajinkyaautade09/jenkins-agent-maven:latest
    command: [ "cat" ]
    tty: true
"""
        }
    }

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
                sh 'mvn clean package'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
                        sh "docker build -t ajinkyaautade09/my-java-app:latest ."
                        sh "docker push ajinkyaautade09/my-java-app:latest"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl create namespace gamma || true
                helm upgrade --install my-java-app ./my-java-app-chart -n gamma \
                  --set image.repository=ajinkyaautade09/my-java-app --set image.tag=latest
                """
            }
        }
    }
}
