pipeline {
    agent any
    environment {
        
        def git_branch = 'master'
        def git_url = 'https://github.com/avidere/Pythonapp-deployment.git'
    }
    stages {
        stage('Git Checkout') {
            steps {
                script {
                    git branch: "${git_branch}", url: "${git_url}"
                    echo 'Git Checkout Completed'
                }
            }
        }
        stage('Build  and Test Code') {
            steps {
                script {
                  //  sh 'python3 rsvp.py'
                    sh 'python3 -m pytest'
                }
            }
        }    
		stage('Build Docker image and push To Docker hub'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'docker_pass', usernameVariable: 'docker_user')]) {
                script{
                    sshagent(['Docker-Server']) {
                          //sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo rm -r Pythonapp-deployment"
                          sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 git clone ${git_url} "

       
                          sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo sed -i 's/tag/${env.BUILD_NUMBER}/g' /home/dockeradmin/Pythonapp-deployment/web_deployment.yaml"
                         // sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo cp /home/ubuntu/Pythonapp-deployment/*.yaml /home/ubuntu/"
                          sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 docker build -t avinashdere99/python:${env.BUILD_NUMBER} /home/dockeradmin/Pythonapp-deployment/."
                          sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 docker login -u $docker_user -p $docker_pass"
                       //   sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo rm -r Pythonapp-deployment "
                          sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 docker push avinashdere99/python:${env.BUILD_NUMBER}"
                          sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 docker rmi avinashdere99/python:${env.BUILD_NUMBER}"
                    }
                  }
                }
            }
        }
        stage('Deploy Application on EKS Cluster'){
            steps{
                script{
                    sshagent(['Docker-Server']) {
                        sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo kubectl apply -f Pythonapp-deployment "
                        sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo kubectl get po"
                        sh "ssh -o StrictHostKeyChecking=no -l dockeradmin 172.31.2.23 sudo kubectl get all "
                        
                    }
                }
            }
        } 
    }
}
