pipeline {
    agent any

    environment {
        // KUBECONFIG points to the Jenkins user's kubeconfig location
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
        // Use python3 explicitly to avoid confusion with python or python2
        PATH = "${env.WORKSPACE}/venv/bin:${env.PATH}"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Samyakshrma/orbis-python.git'
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
                    // Tag image with build number to avoid overwriting previous images
                    def imageTag = "orbis-python:${env.BUILD_NUMBER}"
                    docker.build(imageTag)
                    // Optionally, push to a registry here if needed
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Make sure kubeconfig is set and accessible by kubectl
                sh '''
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}