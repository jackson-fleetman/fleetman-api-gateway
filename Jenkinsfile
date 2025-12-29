pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)

     SERVICE_NAME = "fleetman-api-gateway"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
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

               export MAVEN_HOME=/tmp/maven/apache-maven-3.9.12
               export PATH=$PATH:$MAVEN_HOME/bin
               mvn --version
               
               sh '''mvn clean package'''
            }
         }
      }
      
      stage('Build and Push Image') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
         }
      }

      stage('Deploy to Cluster') {
          steps {
                    // sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
                    // The above command did not work due to authentication errors - hence add validate=false to bypass authentication for testing only
                     sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f - --validate=false'
          }
      }
   }
}
