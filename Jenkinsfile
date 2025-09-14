pipeline {
    agent any

    environment {
        AWS_REGION = "us-west-2"
        ECR_REPO = "812073016710.dkr.ecr.us-west-2.amazonaws.com/test-app"
        IMAGE_TAG = "latest"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
        NAMESPACE = "your-namespace" // change or remove if you want default
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/lakshmimakineni25-sys/Pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $ECR_REPO:$IMAGE_TAG ."
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin $ECR_REPO
                docker push $ECR_REPO:$IMAGE_TAG
                """
            }
        }

        stage('Deploy to EKS') {
            steps {
                withEnv(["KUBECONFIG=$KUBECONFIG"]) {
                    // Create namespace if not default
                    sh "kubectl get namespace $NAMESPACE || kubectl create namespace $NAMESPACE"
                    
                    // Apply deployment and service
                    sh "kubectl apply -f ${WORKSPACE}/k8s/deployment.yaml -n $NAMESPACE"
