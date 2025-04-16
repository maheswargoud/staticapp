pipeline {
    agent any

    stages {
        stage('CheckOut') {
            steps {
                git branch: 'main', url: 'https://github.com/maheswargoud/staticapp'
            }
        }
        stage('DockerLogin') {
            steps {
                sh 'aws ecr get-login-password --region ap-south-1 | sudo docker login --username AWS --password-stdin 355195805640.dkr.ecr.ap-south-1.amazonaws.com'
            }
        }
        stage('Build') {
            steps {
                sh 'sudo docker build -t 355195805640.dkr.ecr.ap-south-1.amazonaws.com/ecr-pipeline:$BUILD_NUMBER .'
            }
        }
        stage('Push') {
            steps {
                sh 'sudo docker push 355195805640.dkr.ecr.ap-south-1.amazonaws.com/ecr-pipeline:$BUILD_NUMBER'
            }
        }
        stage('Replace build number') {
            steps {
                sh 'sed -i s/number/$BUILD_NUMBER/g k8s/deployment.yml'
            }
        }
        stage('Namespace') {
            steps {
                sh 'kubectl apply -f k8s/namespace.yml'
            }
        }
        stage('Deployment') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yml'
            }
        }
        stage('Service') {
            steps {
                sh 'kubectl apply -f k8s/service.yml'
            }
        }
        stage('Ingress') {
            steps {
                sh 'kubectl apply -f k8s/ingress.yml'
            }
        }
        stage('Ingress endpoint') {
            steps {
                sh 'kubectl get ingress -n k8s-pipeline'
            }
        }
        stage('Ingress pod') {
            steps {
                sh 'kubectl get po -n ingress-nginx'
            }
        }
        // stage('Ingress describe') {
        //     steps {
        //     sh 'kubectl describe po ingress-nginx-controller-6dc4c69456-7lqwf -n ingress-nginx'
        //         }
        // }
    }
}
