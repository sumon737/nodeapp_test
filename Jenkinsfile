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
    
    stage('Checking Deployment Files in Github') {
      steps {
        sh 'ls -a'
        echo 'cat Before:'
        sh 'cat deploymentserviceingress.yaml'
        echo 'Changing with latest Build Number:'
        sh 'cat deploymentserviceingress.yaml | sed "s/{{BUILD_NUMBER}}/${env.BUILD_NUMBER}/g" '
        sh 'git status'
        //sh 'kubectl rollout restart deploy nodeapp-deployment -n develop'
        echo 'cat After:'
        sh 'cat deploymentserviceingress.yaml'
        
      }
    }
    
    stage('Apply Kubernetes Files') {
      steps {
        withKubeConfig([credentialsId: 'su-local-k8s', serverUrl: 'https://haproxy-lb:6443', namespace: 'develop']) {
          sh 'kubectl get nodes'
          sh 'kubectl get ns'
          sh 'kubectl apply -f deploymentserviceingress.yaml'
          sh 'kubectl get pods -n develop'
          echo 'Done, Thanks!'
        }
      }
    }
  }
}
