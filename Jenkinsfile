pipeline {
  agent any

  environment {
    ZAP_API_URL = "http://192.168.119.28:8080"
    API_KEY = "myapikey"
    TARGET_URL = "http://192.168.119.28:5540"
    REPORT_NAME = "zap_report.html"    
  }

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
        sh 'git status'
        //sh 'kubectl rollout restart deploy nodeapp-deployment -n develop'
        echo 'cat After:'
        sh 'cat deploymentserviceingress.yaml'
        sh 'cat istio-deploy-svc-vs-gw.yaml'
      }
    }

    stage('Apply Kubernetes Files') {
      steps {
        withKubeConfig([credentialsId: 'su-local-k8s', serverUrl: 'https://192.168.122.90:6443', namespace: 'develop']) {
          sh 'kubectl get nodes'
          sh 'kubectl get ns'
          //sh 'kubectl apply -f deploymentserviceingress.yaml'
          //sh 'cat deploymentserviceingress.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f - '
          //sh 'cat istio-deploy-svc-vs-gw.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f - '
          sh 'kubectl get pods -n develop'
          echo 'Done, Thanks!'
        }
      }
    }

    stage('Trigger ZAP Scan') {
            steps {
                script {
                    sh '''
                    curl -X GET "$ZAP_API_URL/JSON/spider/action/scan/?apikey=$API_KEY&url=$TARGET_URL&maxChildren=10"
                    '''
                }
            }
        }

    stage('Wait for Scan Completion') {
            steps {
                script {
                    def status = ""
                    while (status != "100") {
                        sleep 10
                        status = sh(script: "curl -s $ZAP_API_URL/JSON/spider/view/status/?apikey=$API_KEY | jq -r '.status'", returnStdout: true).trim()
                        echo "Scan progress: $status%"
                    }
                }
            }
        }

    stage('Generate ZAP Report') {
            steps {
                script {
                    sh '''
                    curl -X GET "$ZAP_API_URL/OTHER/core/other/htmlreport/?apikey=$API_KEY" -o $REPORT_NAME
                    '''
                }
            }
        }




    
  }
}
