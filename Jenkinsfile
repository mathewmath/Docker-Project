pipeline {

  environment {
    registry = "mathewmath/flask"
    registry_mysql = "mathewmath/mysql"
    dockerImage = ""
  }

  agent any
    stages {
  
    stage('Checkout Source') {
      steps {
        git 'https://github.com/mathewmath/Docker-Project.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          withDockerRegistry([ credentialsId: "dockerhub" ])  { 
            dockerImage.push()
          }
        }
      }
    }

    stage('current') {
      steps{
        dir("${env.WORKSPACE}/mysql"){
          sh "pwd"
          }
      }
   }
      stage('Build mysql image') {
     steps{
       withDockerRegistry([ credentialsId: "dockerhub" , url: "" ]) {
       sh 'docker build -t "mathewmath/mysql:$BUILD_NUMBER"  "$WORKSPACE"/mysql'
        sh 'docker push "mathewmath/mysql:$BUILD_NUMBER"'
        }
      }
}
   
      stage('Push MySQL Image') {
      steps{
        script {
          withDockerRegistry([ credentialsId: "dockerhub", url: "" ]) {
            dockerImage.push("registry_mysql")
          }
        }
      }
    }
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "frontend.yaml", kubeconfigId: "kube")
        }
      }
    }

  }

}
