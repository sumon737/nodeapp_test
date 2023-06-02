pipeline {
  agent none

  stages {
    stage('Docker Build') {
      agent any
      steps {
        sh 'docker build -t sumon737/nodeapp:${env.BUILD_NUMBER} .'
      }
    }

    stage('Docker Push') {
      agent any
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push sumon737/nodeapp:${env.BUILD_NUMBER}'
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
        withKubeConfig([credentialsId: 'kubernetes-id']) {
          sh 'ls -a'
          sh 'cat deploymentservice.yml | sed "s/{{BUILD_NUMBER}}/${env.BUILD_NUMBER}/g" | kubectl apply -f -'
        }
      }
    }
  }
}
