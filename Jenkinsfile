pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "225201316516.dkr.ecr.us-east-1.amazonaws.com/my-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Masrwahaj/my-repo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t my-app .'
            }
        }

        stage('Login ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region us-east-1 \
                | docker login --username AWS --password-stdin 225201316516.dkr.ecr.us-east-1.amazonaws.com
                '''
            }
        }

        stage('Tag') {
            steps {
                sh "docker tag my-app:latest $ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Push') {
            steps {
                sh "docker push $ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }

    stage('Configure kubeconfig') {
    steps {
        sh '''
        aws eks update-kubeconfig \
        --region us-east-1 \
        --name devops-cluster
        '''
    }
}

    post {
        always {
            sh '''
            docker container prune -f
            docker image prune -f
            '''
        }
    }
}
