# Overview
This project is to setup a modern kubernetes stack with two main clusters of seperation:
* Top-zone - A cluster dedicated to front end applications
* Mid-zone - A cluster dedicated to backend apis

This seperation ensures that users direct access to the top-zone clusters terminates there. Communication to the apis behaves in a machine to machine flow. 

Istio will be the ingress of choice and with it's side car mode setup all pods can communicate to each other securely via mutal tls.

# Requirements
* [Minikube](https://minikube.sigs.k8s.io/docs/)
* [Kubectl](https://kubernetes.io/docs/tasks/tools/)
* [Kubectx + Kubens](https://github.com/ahmetb/kubectx)
* [Istioctl](https://istio.io/latest/docs/setup/additional-setup/download-istio-release/)

# Setup
For the setup it is important to use hyperv, this makes it exteremly easy to pull containers from your local docker repository. Using a CPU count of 4 is the minimum requirement for the loadbalancer and ingress to function properly.

## Setting Up Top-zone
1. Create the cluster  
`minikube start --profile top-zone --driver=hyperv --cpus=4`

2. Install the load balancer (Azure would manage this for you)  
`minikube -p top-zone addons enable metallb`  

3. Grab the ip  for use in the next step  
`minikube -p top-zone ip`

4. Based on the ip enter an approriate range
`minikube -p top-zone addons configure metallb`

5. Install istio side car setup [(here)](https://istio.io/latest/docs/setup/install/istioctl/)

## Setting Up Mid-zone
1. Create the cluster  
`minikube start --profile mid-zone --driver=hyperv --cpus=4`

2. Install the load balancer (Azure would manage this for you)  
`minikube -p mid-zone addons enable metallb`  

3. Grab the ip  for use in the next step  
`minikube -p mid-zone ip`

4. Based on the ip enter an approriate range
`minikube -p mid-zone addons configure metallb`

## Container Registry
I'm using githubs artifact repository, you will have to do something similar for other

1. Register your classic PAT in kube
```kubectl create secret docker-registry gh-regcred \
  --docker-server=ghcr.io \
  --docker-username=<YOUR_GITHUB_USERNAME> \
  --docker-password=<YOUR_PAT> \
  --docker-email=<YOUR_GITHUB_EMAIL>
  ```
