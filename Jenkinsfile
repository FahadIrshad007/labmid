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

        stage('Setup Config') {
            steps {
                sh 'cp configs/FA23-BAI-014_config.json config.json'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'python3 -m pip install --break-system-packages -r requirements.txt || python3 -m pip install -r requirements.txt'
            }
        }

        stage('Generate Dataset if Missing') {
            steps {
                sh '''
                    if [ ! -f "dataset/train.csv" ]; then
                        python3 generate_dataset.py
                    fi
                '''
            }
        }

        stage('Train Model') {
            steps {
                sh 'python3 train.py'
            }
        }

        stage('Deploy via SSH to EC2') {
            steps {
                sh '''
                    ssh -i /tmp/jenkins_ec2 -o StrictHostKeyChecking=no ubuntu@172.31.16.105 "
                        cd ~/labmid &&
                        git pull origin main &&
                        docker rm -f ml_app || true &&
                        docker build -t ml_app . &&
                        docker run -d -p 8000:8000 --name ml_app ml_app
                    "
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed! API running at port 8000.'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}