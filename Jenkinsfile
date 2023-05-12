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
                
                    sh 'docker login -u "harshr69" -p "Harshhclmi5" hub.docker.com'
                    sh 'docker tag my-docker-image hub.docker.com/my-docker-image'
                    sh 'docker push hub.docker.com/my-docker-image'
                
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
