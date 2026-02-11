pipeline {
    agent any

    stages {
        stage('Checkout from GitHub') {
            steps {
                git branch: 'dev', 
                    url: 'https://github.com/Geetha0307/spring-boot-mongo-docker-kkfunda-cd.git'
            }
        }

        stage('Setup KubeConfig') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-eks-cred']]) {
                    sh '''
                        aws eks update-kubeconfig --region us-east-1 --name my-cluster
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-eks-cred']]) {
                    sh '''
                        kubectl apply -f springBootMongo.yml --validate=false
                    '''
                }
            }
        }

      stage('Verify Pods and Services') {
    steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                          credentialsId: 'aws-eks-cred']]) {
            sh '''
                echo "Waiting for pods to be ready..."
                kubectl wait --for=condition=Ready pods --all --timeout=120s

                echo "Pods status:"
                kubectl get pods -o wide

                echo "Services status:"
                kubectl get svc
            '''
                    }
            }
        }

    }
}
