pipeline {
  environment {
    registry = "nico916/projet-m2"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }

  agent any

  stages {
    stage('Git checkout') {
      steps {
         checkout scm
      }
    }
    
    stage('Building image') {
      steps{
          dir ( 'app'){
          script {
           dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }}
    }
    stage('Publish Image ') {
      steps{
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            dockerImage.push("latest")
          }
           echo "trying to push Docker Build to DockerHub"
        }
      }
    }

    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
    stage('Deploy K8s') {
      steps{
	   script {
      def configs = readFile 'deployment-apache.yml'
      def services = readFile 'service-apache.yml'

      sh 'minikube start --force'
      sh 'minikube status'
      sh 'kubectl config use-context minikube'
      sh "echo '${configs}' | kubectl apply -f -"
      sh "echo '${services}' | kubectl apply -f -"
      }
				}
    }
}
  
}
