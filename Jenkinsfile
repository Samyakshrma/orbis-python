pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/Samyakshrma/orbis-python.git'
            }
        }

        stage('Setup and Test') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install pytest
                    echo "Running tests..."
                    pytest tests/test_dockerfile_generator.py -v
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("orbis-python")
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

