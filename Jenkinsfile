pipeline {
  environment {
    dockerimagename = "narayanacharan/react-app-00"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      agent {
                docker {
                    image 'narayanacharan/jenkins-slave:7.0'
                    label 'java-docker-slave'                    
                }
      }
      steps {
          git credentialsId: 'github', url: 'https://github.com//charanbsre/jenkins_assignment.git'
      }
    }
    
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
   
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([string(credentialsId: 'kubetext', variable: 'KUBECONFIG')]) {
                    sh '''
                        mkdir -p /home/jenkins/.kube
                        touch /home/jenkins/.kube/config
                        echo 'kubetext' > /home/jenkins/.kube/config
                        kubectl apply -f bluewhale.yml
                    '''
        }
      }
    }
  }
}
