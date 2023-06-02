pipeline {
  agent any
  stages {
    stage('Docker Build') {
      steps {
        sh "docker build -t sumon737/nodeapp:${env.BUILD_NUMBER} ."
      }
    }
    stage('Docker Push') {
      steps {
        withDockerRegistry([usernamePassword(credentialsId: 'sumon737-dockerhub', passwordVariable: 'sumon737-dockerhubPassword', usernameVariable: 'sumon737-dockerhubUser')]) {
          sh "docker login -u ${env.sumon737-dockerhubUser} -p ${env.sumon737-dockerhubPassword}"
          sh "docker push sumon737/nodeapp:${env.BUILD_NUMBER}"
        }
      }
    }
    ---
          stage('Build image') {
       dockerImage = docker.build("monishavasu/my-react-app:latest")
    }
    
 stage('Push image') {
        withDockerRegistry([ credentialsId: "dockerhubaccount", url: "" ]) {
        dockerImage.push()
        }
    }    
}
    ---  
    
    stage('Docker Remove Image') {
      steps {
        sh "docker rmi sumon737/nodeapp:${env.BUILD_NUMBER}"
      }
    }
    stage('Apply Kubernetes Files') {
      steps {
          withKubeConfig([credentialsId: 'kubernetes-id']) {
              sh 'ls -a'
              //sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'
              //sh 'chmod u+x ./kubectl'
              //sh './kubectl get pods -n dev'
              //sh './kubectl get pods -n dev'
              //sh 'cat deploymentservice.yml'
              sh 'cat deploymentservice.yml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | ./kubectl apply -f -'
        }
      }
  }
}
}
