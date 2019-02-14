pipeline {
    agent none
    environment {
        imageName = 'YOUR_IMAGE_NAME'
        port = 3030
    }
    
    stages {
       stage('Build') { 
          agent any
          steps {
              sh "docker --version"
              sh "docker build -t ${env.imageName} ."
          }
       }

       stage('Package') { 
          agent {label 'mgr1'}
          steps {
            withCredentials(
                [usernamePassword(
                    credentialsId: 'docker_hub', 
                    passwordVariable: 'dockerHubPassword', 
                    usernameVariable: 'dockerHubUser'
                )]
            ){
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                sh "docker push ${env.imageName}"
            }
          }
       }

       stage('Deploy') { 
          agent {label 'mgr1'}
          steps {
              script {
                  try {
                    sh "docker service update --image ${env.imageName} demoApi"
                    sh "echo update service"
                  } catch (e){
                    sh "docker service create --name demoApi -p ${env.port}:3000 ${env.imageName}"
                    sh "echo create service"
                  }
              }
          }
       }
    }
}