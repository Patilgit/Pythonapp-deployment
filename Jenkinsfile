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
		 stage('Build Image') {
      steps {
        echo "===Building docker image==="
        sh 'docker build -t darshan626/new-repo:latest1 .'
      } 
    }
      stage('Docker Build and Push') {
      steps {
      	withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        	sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
        	sh 'docker push darshan626/new-repo:latest2'
    }
}
}
      stage ('eks deployment') {
          steps {
             sh 'kubectl apply -f web_deployment.yaml'
             sh 'kubectl apply -f web_service.yaml'
             sh 'kubectl apply -f mongo_deployment.yaml'
             sh 'kubectl apply -f mongo_service.yaml'
             sh 'kubectl apply -f kubernetes_secret.yaml'
             sh 'kubectl apply -f db_configmap.yaml'
               }
      }
    }
}
