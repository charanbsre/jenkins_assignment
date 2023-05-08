pipeline {
  environment {
    dockerimagename = "narayanacharan/react-app-00"
    dockerImage = ""
  }
  agent none
  stages {
    stage('Checkout Source') {
      agent {
                docker {
                    image 'narayanacharan/jenkins-slave:2.0'
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
    
    stage('Pushing Image') {
      environment {
               registryCredential = 'dhub'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Copy kubeconfig') {
            steps {
                sh 'mkdir ~/.kube'
                copy {
                    from: '/var/lib/jenkins/workspace/pipeProj/config',
                    to: '~/.kube/config'
                }
            }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://nextopsaks01-dns-g49pt1dh.hcp.eastus.azmk8s.io']) {
            kubernetesDeploy(
            kubeconfigId: 'kubeconfig',
            configs: 'hellowhale.yml',
            enableConfigSubstitution: true,
            recreate: true
          )
        }  
      }
    }
  }
}
