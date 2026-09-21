pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'   // change if needed
        ACCOUNT_ID = '225201316516'
        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/my-app"
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

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag my-app:latest $ECR_REPO:latest'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $ECR_REPO:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop my-app || true
                docker rm my-app || true
                docker run -d -p 80:80 --name my-app my-app
                '''
            }
        }
    }
}
