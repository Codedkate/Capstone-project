pipeline {
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t jen .'
                  sh 'docker image ls'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "jenhub"]) {
                      sh 'docker tag jen catherine247/jen'
                      sh 'docker push catherine247/jen'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws', region: 'us-east-1') {
                      sh "aws eks --region us-east-1 update-kubeconfig --name jen"
                      sh "kubectl config use-context arn:aws:eks:us-east-1:076519505878:cluster/jen"
                      sh "kubectl apply -f deployment.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/jen"
                  }
              }
        }
        stage("Cleaning dangling files") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune -f"
              }
        }
     }
}

          
