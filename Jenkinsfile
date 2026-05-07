pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Fetch Data from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/FahadIrshad007/labmid.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Train Model') {
            steps {
                sh 'python3 train.py'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker rm -f ml_app || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ml_app .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8000:8000 --name ml_app ml_app'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully! API is running at port 8000.'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}
