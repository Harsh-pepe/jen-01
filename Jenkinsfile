pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t my-docker-image .'
            }
        }
        
        stage('Test') {
            steps {
                sh 'docker run my-docker-image python3 -m unittest discover'
            }
        }
        
        stage('Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-registry-cred-id', variable: 'DOCKER_REGISTRY_CRED')]) {
                    sh 'docker login -u "$DOCKER_REGISTRY_USERNAME" -p "$DOCKER_REGISTRY_PASSWORD" my-docker-registry.com'
                    sh 'docker tag my-docker-image my-docker-registry.com/my-docker-image'
                    sh 'docker push my-docker-registry.com/my-docker-image'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sshagent(credentials: ['my-ec2-instance-cred-id']) {
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@my-ec2-instance 'sudo docker stop my-docker-container && sudo docker rm my-docker-container || true'"
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@my-ec2-instance 'sudo docker run -d --name my-docker-container -p 80:5000 my-docker-registry.com/my-docker-image'"
                }
            }
        }
    }
}
