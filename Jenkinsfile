pipeline {

  environment {
    dockerimagename = "sumon737/nodeapp"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/sumon737/nodeapp_test.git'
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
               registryCredential = 'dockerlogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("${env.BUILD_NUMBER}")
          }
        }
      }
    }



    stage('Deploying App to Kubernetes') {
      echo "triggering updatemanifestjob"
      build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]

      steps {
        script {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        //def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                        sh "git config user.email sumon737@gmail.com"
                        sh "git config user.name sumon737"
                        sh "cat deploymentservice.yml"
                        sh "sed -i 's+sumon737/nodeapp_test.*+sumon737/nodeapp_test:${DOCKERTAG}+g' deploymentservice.yaml"
                        sh "cat deploymentservice.yml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/nodeapp_test.git HEAD:master"          
                        kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
        }
      }
    }

  }

}
