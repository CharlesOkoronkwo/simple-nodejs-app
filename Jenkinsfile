pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/CharlesOkoronkwo/simple-nodejs-app.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('simple-nodejs-app')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'taylorbree') {
                        docker.image('simple-nodejs-app').push()
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                // Example: Deploy to a Kubernetes cluster
                sh 'kubectl apply -f k8s/deployment.yaml --namespace staging'
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
