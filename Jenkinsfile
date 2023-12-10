pipeline {

    agent {
        label 'agentDocker'
    }

    environment {
        AWS_EKS_CLUSTER_NAME = 'cluster4v4'
        AWS_EKS_REGION = 'us-east-1'
        KUBE_MANIFESTS_DIR = '/home/ubuntu/Final4Deployment/KUBE_MANIFEST'
    }

    stages {
        stage('Build Images') {
            steps {
                sh 'docker-compose build'
            }
        }
        stage('Login and Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dannydee93-dockerhub', usernameVariable: 'DOCKERHUB_CREDENTIALS_USR', passwordVariable: 'DOCKERHUB_CREDENTIALS_PSW')]) {
                    sh "echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin"
                    sh 'docker push dannydee93/eshopwebmvc'
                    sh 'docker push dannydee93/eshoppublicapi'       
                }
            }
        }
                    

        stage('Deploy to EKS') {

            agent {
                label 'agentEKS'
            }

            steps {

                dir('KUBE_MANIFEST') {

                    script {

                        withCredentials([
                            string(credentialsId: 'AWS_ACCESS_KEY', variable: 'aws_access_key'),
                            string(credentialsId: 'AWS_SECRET_KEY', variable: 'aws_secret_key')
                        ]) {
                            sh "aws eks --region $AWS_EKS_REGION update-kubeconfig --name $AWS_EKS_CLUSTER_NAME"
                            sh "kubectl apply -f $KUBE_MANIFESTS_DIR"
                        }

                    }

                }

            }

        }

    }







