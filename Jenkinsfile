pipeline {
    agent any

    environment {
        registry = "taylorbree/simple-nodejs-app" // Replace with your Docker Hub repository
        registryCredential = 'taylorbree' // Jenkins credential ID for Docker Hub
        dockerImage = ''
        kubernetesNamespace = 'staging' // Replace with your Kubernetes namespace
        kubernetesDeploymentName = 'simple-nodejs-app' // Replace with your Kubernetes deployment name
    }

    stages {

        stage('Checkout') {
            steps {
                // Checkout the code from your GitHub repository
                git branch: 'docker-integration-pipeline', url: 'https://github.com/CharlesOkoronkwo/simple-nodejs-app.git', credentialsId: 'Git-Hub-Credentials'
            }
        }

        stage('Build') {
            steps {
                // Install dependencies
               sh 'curl -sL https://deb.nodesource.com/setup_14.x | bash -'
               sh 'apt-get install -y nodejs'
               sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image and tag it as 'latest'
                    dockerImage = docker.build registry + ":latest"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to Docker Hub and push the image
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy the application to Kubernetes
                    sh """
                    kubectl set image deployment/${kubernetesDeploymentName} ${kubernetesDeploymentName}=${registry}:latest --namespace=${kubernetesNamespace}
                    kubectl rollout status deployment/${kubernetesDeploymentName} --namespace=${kubernetesNamespace}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs for details.'
        }
    }
}

