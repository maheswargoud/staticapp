pipeline {
    agent any

    stages {
        stage('CheckOut') {
            steps {
                git branch: 'main', url: 'git@github.com:maheswargoud/staticapp.git'
            }
        }
        stage('DockerLogin') {
            steps {
                sh 'aws ecr get-login-password --region ap-southeast-1 | sudo docker login --username AWS --password-stdin 706339012951.dkr.ecr.ap-southeast-1.amazonaws.com'
            }
        }
        stage('Build') {
            steps {
                sh 'sudo docker build -t 706339012951.dkr.ecr.ap-southeast-1.amazonaws.com/staticapp:$BUILD_NUMBER .'
            }
        }
        stage('Push') {
            steps {
                sh 'sudo docker push 706339012951.dkr.ecr.ap-southeast-1.amazonaws.com/staticapp:$BUILD_NUMBER'
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
