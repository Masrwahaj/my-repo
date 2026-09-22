pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-app"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Masrwahaj/my-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker stop my-app || true'
                sh 'docker rm my-app || true'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 80:80 --name my-app $IMAGE_NAME'
            }
        }
    }
}
