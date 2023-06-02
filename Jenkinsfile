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
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push sumon737/nodeapp:${env.BUILD_NUMBER}"
        }
      }
    }

    stage('Docker Remove Image') {
      steps {
        sh "docker rmi sumon737/nodeapp:${env.BUILD_NUMBER}"
      }
    }

    stage('Apply Kubernetes Files') {
      steps {
        withKubeConfig([credentialsId: 'su-local-k8s']) {
          sh 'ls -a'
          sh 'whoami'
          //sh 'cat deploymentservice.yml | sed "s/{{BUILD_NUMBER}}/${env.BUILD_NUMBER}/g" | kubectl apply -f -'
          //sh 'cat deploymentservice.yml | sed "s/{{BUILD_NUMBER}}/${env.BUILD_NUMBER}/g"'
          sh 'kubectl get nodes'
          sh 'kubectl get ns'
        }
      }
    }
  }
}
