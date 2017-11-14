## Hello world kubernetes app

Based on this tutorial

    - https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/#objectives

Start the Minikube cluster:

    $ minikube start --vm-driver=xhyve


Now set the Minikube context. The context is what determines which cluster kubectl is interacting with. You can see all your available contexts in the ~/.kube/config file:

    $ kubectl config use-context minikube

    $ kubectl cluster-info

### Create a Docker container image

Preview the sample node server.js app. You can cerify that it runs fine:

    $ node server.js

Because this tutorial uses Minikube, instead of pushing your Docker image to a registry, you can simply build the image using the same Docker host as the Minikube VM, so that the images are automatically present. To do so, make sure you are using the Minikube Docker daemon:

    $ eval $(minikube docker-env)

Note: Later, when you no longer wish to use the Minikube host, you can undo this change by running `eval $(minikube docker-env -u)`

Build your Docker image, using the Minikube Docker daemon:

    $ docker build -t hello-node:v1 .

### Create a Deployment

A Kubernetes Pod is a group of one or more Containers, tied together for the purposes of administration and networking. The Pod in this tutorial has only one Container. A Kubernetes Deployment checks on the health of your Pod and restarts the Pod’s Container if it terminates. Deployments are the recommended way to manage the creation and scaling of Pods.

Use the kubectl run command to create a Deployment that manages a Pod. The Pod runs a Container based on your hello-node:v1 Docker image:

    $ kubectl run hello-node --image=hello-node:v1 --port=8080

View options:

    $ kubectl get deployments   # View the Deployment

    $ kubectl get pods          # View the Pod

    $ kubectl get events        # View cluster events

    $ kubectl config view       # View the kubectl configuration


### Create a Service
By default, the Pod is only accessible by its internal IP address within the Kubernetes cluster. To make the hello-node Container accessible from outside the Kubernetes virtual network, you have to expose the Pod as a Kubernetes Service.

From your development machine, you can expose the Pod to the public internet using the kubectl expose command:

    $ kubectl expose deployment hello-node --type=LoadBalancer

View the Service you just created:

    $ kubectl get services

On Minikube, the LoadBalancer type makes the Service accessible through the minikube service command.

    $ minikube service hello-node



### Updating the app

Assuming the app was modified, build a new version of your image:

    $ docker build -t hello-node:v2 .

Update the image of your Deployment:

    $ kubectl set image deployment/hello-node hello-node=hello-node:v2

Run your app again to view the new message:

    $ minikube service hello-node

### Clean up

    $ kubectl delete service hello-node

    $ kubectl delete deployment hello-node

Optionally, stop Minikube:

    $ minikube stop







