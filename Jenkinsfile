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
        withKubeConfig([credentialsId: 'su-local-k8s', serverUrl: 'https://haproxy-lb:6443', namespace: 'develop']) {
          sh 'kubectl get nodes'
          sh 'kubectl get ns'
        }
      }
    }
  }
}
