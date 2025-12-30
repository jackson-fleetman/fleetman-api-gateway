pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)

     SERVICE_NAME = "fleetman-api-gateway"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

   tools { 
         maven 'maven 3.9.12' 
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      
      stage('Build') {
         steps {
               withMaven(maven: 'maven 3.9.12') { // Use the name configured in Global Tool Configuration to find the correct MAVEN_HOME
                  sh '''export MAVEN_HOME=/tmp/maven/apache-maven-3.9.12'''
                  sh '''export PATH=$PATH:$MAVEN_HOME/bin'''
                  sh '''/tmp/maven/apache-maven-3.9.12/bin/mvn --version'''
                  
                  // sh '''./mvn clean package'''
                  sh '''/tmp/maven/apache-maven-3.9.12/bin//mvn clean package'''
               }
         }
      }
      
      stage('Build and Push Image') {
         // agent {
         //    docker {
                // Set both label and image
                // label 'docker'
                // image 'alpine/socat'
                // list any args
                // args '--name docker-node' 
         //    }
         // }
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
         }
      }
      
      stage('Deploy to Cluster') {
          steps {
                     // Install kubectl
                     sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
                     sh 'chmod +x ./kubectl'

                     // Install envsubst
                     sh 'curl -LO "https://github.com/a8m/envsubst/releases/download/v1.2.0/envsubst-`uname -s`-`uname -m` -o envsubst"'
                     sh 'chmod +x ./envsubst'

                     // sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
                     // The above command did not work due to authentication errors - hence add validate=false to bypass authentication for testing only

                     // ./ makes a difference; otherwise command not found error will be encountered
                     sh './envsubst < ${WORKSPACE}/deploy.yaml | ./kubectl apply -f - --validate=false'
                     // sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f - --validate=false'
                  
          }
      }
   }
}


