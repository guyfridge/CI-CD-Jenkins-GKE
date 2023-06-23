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
3. Edit the 'CLUSTER_ZONE' value inside /sample-app/Jenskinsfile to reflect the desired region for your GKE cluster
`vi /sample-app/Jenkinsfile`
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
4. Create the GKE cluster using the gcloud tool and specify the relevant region for the cluster
```
gcloud container clusters create jenkins-cd \
  --zone us-west1-a --scopes cloud-platform
```
5. Confirm that you can connect to your cluster
`kubectl cluster-info`
6. Create a ClusterRole and ClusterRoleBinding in GKE's RBAC to make your account a cluster administrator so that you can give Jenkins permissions in the cluster
```
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole=cluster-admin --user=$(gcloud config get-value account)
```
## Use Helm to install Jenkins on GKE
1. Add the Jenkins Helm repository
```
helm repo add jenkinsci https://charts.jenkins.io
helm repo update
```
2. The Jenkins installation is configured via the custom jenkins/values.yaml file. Use Helm CLI to deploy Jenkins using your custom configuration.
`helm install cd-jenkins -f jenkins/values.yaml jenkinsci/jenkins --wait`
3. Make sure the Jenkins pod is in a 'Running' state and the containers are in a 'Ready' state.
`kubectl get pods`
Under 'READY' you should see '2/2' and under 'STATUS' you should see 'Running'
```
NAME           READY   STATUS    RESTARTS   AGE
cd-jenkins-0   2/2     Running   0          6m30s
```
5. Verify that the Kubernetes services were created
`kubectl get svc`
```
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
cd-jenkins         ClusterIP   10.124.15.149   <none>        8080/TCP    12m
cd-jenkins-agent   ClusterIP   10.124.11.222   <none>        50000/TCP   12m
kubernetes         ClusterIP   10.124.0.1      <none>        443/TCP     51m
```


# Resources
1. https://docs.bitnami.com/tutorials/create-ci-cd-pipeline-jenkins-gke/
2. https://cloud.google.com/kubernetes-engine/docs/archive/jenkins-on-kubernetes-engine-tutorial
3. https://cloud.google.com/kubernetes-engine/docs/archive/continuous-delivery-jenkins-kubernetes-engine
4. https://cloud.google.com/architecture/using-jenkins-for-distributed-builds-on-compute-engine
