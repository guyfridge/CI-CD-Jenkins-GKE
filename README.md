# CI-CD with Jenkins, Docker, and GKE
Create a CI-CD pipeline for testing and deployment of containerized applications using Jenkins, Docker, and GKE

## Prerequisites
1. Create a new project inside your Google Cloud account
2. Enable the following APIs in your project: Compute Engine API, Kubernetes Engine API, Cloud Build API
3. Activate Cloud Shell from your project. This gives you command line access in Google Cloud console and is equipped with gcloud CLI as well as other necessary development tools

## Create a GKE cluster
1. Clone the following repository containing Kubernetes manifests for deployment of Jenkins
`git clone https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes.git`
2. Once cloned, change to the /continuous-deployment-on-kubernetes directory
`cd continuous-deployment-on-kubernetes`
3. Edit the /sample-app/Jenskinsfile to reflect the desired region for your GKE cluster
`vi /sample-app/Jenkinsfile` press 'i' to edit the 'CLUSTER_ZONE' value
```
environment {
    PROJECT = "REPLACE_WITH_YOUR_PROJECT_ID"
    APP_NAME = "gceme"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-west1-a"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }
```
press ':wq' to save and quit


# Resources
1. https://docs.bitnami.com/tutorials/create-ci-cd-pipeline-jenkins-gke/
2. https://cloud.google.com/kubernetes-engine/docs/archive/jenkins-on-kubernetes-engine-tutorial
3. https://cloud.google.com/kubernetes-engine/docs/archive/continuous-delivery-jenkins-kubernetes-engine
4. https://cloud.google.com/architecture/using-jenkins-for-distributed-builds-on-compute-engine
