pipeline {
    agent any
    
    environment {
        registry = "400155180151.dkr.ecr.ap-south-1.amazonaws.com/docker-repo"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hades1908/docker-spring-boot.git']])
            }
        }
        stage('Build jar') {
            steps {
                sh "mvn clean install"
            }
        }
        stage('Docker Image Build') {
            steps {
                script {
                    dockerImage = docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        stage('Push Image to ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 400155180151.dkr.ecr.ap-south-1.amazonaws.com"
                    sh "docker push 400155180151.dkr.ecr.ap-south-1.amazonaws.com/docker-repo:$BUILD_NUMBER"
                }
            }
        }
        stage('Helm Deployment') {
            steps {
                script {
                     sh "helm upgrade first --install /var/lib/jenkins/docker-spring-boot/mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
