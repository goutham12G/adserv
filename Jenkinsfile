
pipeline {

  environment {
    PROJECT = "ascendant-timer-350911"
    APP_NAME = "adservice"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "ci-cd"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      inheritFrom 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
 # serviceAccountName: cd-jenkins
  containers:
  - name: gradle-bld
    image: openjdk:latest
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk
    command:
    - cat
    tty: true
  - name: kubectl
    image: google/cloud-sdk
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('codebuild') {
      steps {
        container('gradle-bld') {
          sh """
             ls -a && pwd 
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    } 
    stage('Deploy Dev') {
      steps {
        container('kubectl') {
          sh "gcloud container clusters get-credentials ci-cd --zone us-central1-c --project ascendant-timer-350911"
          sh "kubectl apply -f deployment.yaml"
          sh "kubectl apply -f service.yaml"


          
         
        }
      }
    }
  }
}
