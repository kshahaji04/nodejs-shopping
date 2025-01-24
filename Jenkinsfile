pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'my-node-app'  // Base name of the Docker image
        IMAGE_TAG = "my-node-app:${BUILD_NUMBER}"  // Tag using Jenkins build number
        KUBECONFIG = '/var/lib/jenkins/.kube/config'  // Path to Minikube config
    }
    stages {
        stage('Clone Local Repository') {
            steps {
                script {
                    // Ensure the workspace is clean before cloning
                    sh 'rm -rf /var/lib/jenkins/workspace/my-node-app'
                    
                    // Clone local repository to Jenkins workspace
                    sh 'cp -r /home/sk/sk-test/my-node-app /var/lib/jenkins/workspace/my-node-app'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Navigate to the project directory and build the Docker image with a new tag
                    dir('/var/lib/jenkins/workspace/my-node-app') {
                        sh "docker build -t ${IMAGE_TAG} ."
                    }
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                script {
                    // Update deployment.yaml to use the new image tag
                    sh "sed -i 's|image: my-node-app|image: my-node-app:${BUILD_NUMBER}|' /var/lib/jenkins/workspace/my-node-app/deployment.yaml"
        
                    // Apply both Deployment and Service configurations to Minikube
                    sh 'kubectl apply -f /var/lib/jenkins/workspace/my-node-app/deployment.yaml'
                    sh 'kubectl apply -f /var/lib/jenkins/workspace/my-node-app/service.yaml'

                    // Verify deployment status
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'
                }
            }
        }
    }
}
