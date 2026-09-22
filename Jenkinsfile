pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ACCOUNT_ID = "225201316516"
        ECR_REPO = "225201316516.dkr.ecr.us-east-1.amazonaws.com/my-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Masrwahaj/my-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t my-app ."
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 225201316516.dkr.ecr.us-east-1.amazonaws.com
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh "docker tag my-app:latest $ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Push to ECR') {
            steps {
                sh "docker push $ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh '''
                docker stop my-app || true
                docker rm my-app || true
                docker pull $ECR_REPO:$IMAGE_TAG
                docker run -d -p 80:80 --name my-app $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Health Check') {
    steps {
        sh '''
        sleep 5
        curl -f http://localhost || exit 1
        '''
    }
}
        post {
    success {
        sh 'docker image prune -f'
    }
}
        
    }
}
