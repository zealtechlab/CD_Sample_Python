# Basic operations

## minikube vs microk8s

Minikube runs a single-node Kubernetes cluster inside a VM (e.g. Virtualbox ) in your local development environment. The result is a local Kubernetes endpoint that you can use with the kubectl client.

Microk8s is a new solution for running a lightweight Kubernetes local cluster. It was developed by the Kubernetes team at Canonical. It is designed to be a fast and lightweight upstream Kubernetes installation isolated from your local environment.

## install microk8s

```bash
snap install microk8s --classic

sudo usermod -a -G microk8s $USER

sudo microk8s.enable dns dashboard registry

sudo microk8s.enable registry

```

## Starts MicroK8s and allows provisioning nodes.

```microk8s.start```

## Stops MicroK8s

```microk8s.stop```

## Clear all resources and revert kubernetes to a 'clean' state

```microk8s.reset```

**Note**: user group will be available up on next login.

To Access dashboard

```microk8s dashboard-proxy``` and do not close the terminal. Use the token displayed to login to the dashboard portal.

Command line options - To list all namespaces running 

```bash
sudo microk8s.kubectl get all --all-namespaces
```

To get default token for login (kubernetes-dashboard-token can also be used)

```bash

sudo microk8s.kubectl -n kube-system describe secret $(sudo microk8s.kubectl -n kube-system get secret | grep default-token | awk '{print $1}')

sudo microk8s.kubectl -n kube-system describe secret $(sudo microk8s.kubectl -n kube-system get secret | grep kubernetes-dashboard | awk '{print $1}')
```

To create admin user and get admin-user's token if the default token does not work

```bash
sudo microk8s.kubectl apply -f kube/dashboard-adminuser.yaml

sudo microk8s.kubectl apply -f kube/dashboard-rolebinding.yaml

sudo microk8s.kubectl -n kube-system describe secret $(sudo microk8s.kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```
### ***[OPTION 1 - default]*** Using microk8s registry
As and when registry is enabled in microk8s, a registry server that could be accesses via localhost:32000 already is created.

Tag the images and push to mk8 regitry

```bash
docker tag flaskrblogger:mycicd localhost:32000/flaskrblogger:mycicd
docker push localhost:32000/flaskrblogger:mycicd
```

### ***[OPTION 2]*** Inorder to reference a locally created docker image

The image created is known to Docker. However, Kubernetes is not aware of the image. This is because local Docker daemon is not part of the MicroK8s Kubernetes cluster. Export the built image from the local Docker daemon and “inject” it into the MicroK8s image cache like this:

Execute the below from host machines Terminal, DO NOT DO IN VSCODE TERMINAL

```bash
docker save mynginx > myimage.tar
microk8s ctr image import myimage.tar
```

Example:

```bash
docker save myloginapp:mycicd > myimage.tar && sudo microk8s ctr image import myimage.tar && rm myimage.tar
```

Note that when we import the image to MicroK8s we do so under the k8s.io namespace.

Now we can list the images present in MicroK8s:

```bash
microk8s ctr images ls
```

### ***[OPTION 3]*** OTHER Way of doing this is to create a local registry following below instruction

1) Start a local registry container: [this is done in stack setup already]

```docker run --name localregistry -d -p 15000:5000 --restart=always registry:2```

2) Do docker images to find out the REPOSITORY and TAG of your local image. Then create a new tag for your local image :

```docker tag <local-image-repository>:<local-image-tag> localhost:15000/<local-image-name>```
Example:
```docker tag myloginapp:mycicd localhost:15000/myloginapp:mycicd```
or
```docker tag nginx:latest localhost:15000/nginx:latest```

If TAG for your local image is <none>, you can simply do:

```docker tag <local-image-repository> localhost:15000/<local-image-name>```

3) Push to local registry :

```docker push localhost:15000/<local-image-name>```
Example:
```docker push localhost:15000/nginx:alpine```
or
```docker push localhost:15000/myloginapp:mycicd```

This will automatically add the latest tag to localhost:5000/<local-image-name>. Can check by doing docker images.

4) In yaml file, set imagePullPolicy to IfNotPresent :

```bash
spec:
  containers:
  - name: <name>
    image: localhost:5000/<local-image-name>
    imagePullPolicy: IfNotPresent
```

Execute

```bash
sudo microk8s.kubectl apply -f kube/nginx.yml 
```

## Working with kubernetes

```bash
microk8s start

microk8s.kubectl get nodes
microk8s.kubectl get services

microk8s.kubectl cluster-info

microk8s.kubectl get deployment
microk8s.kubectl get deployments

microk8s.kubectl get pods

microk8s.kubectl get events

microk8s.kubectl config view

microk8s.kubectl get replicaset

microk8s.kubectl get service

microk8s.kubectl get pods -o wide

microk8s.kubectl explain pods
microk8s.kubectl get pods -o wide

***Deploy ngnix App to One of the Nodes of the Cluster***
microk8s.kubectl run my-nginx --image=ngingx:alpine --port=80
microk8s.kubectl get pods

***create deployment***
microk8s.kubectl create deployment LoginWebApp --image=myloginapp:mycicd-0.0.1.RELEASE

***expose a service***
microk8s.kubectl expose deployment LoginWebApp --type=LoadBalancer --port=8080

***Scale up the container***
microk8s.kubectl scale deployment LoginWebApp --replicas=3


microk8s.kubectl delete pod LoginWebApp-58ff5dd898-62l9d
microk8s.kubectl autoscale deployment LoginWebApp --max=10 --cpu-percent=70

microk8s.kubectl edit deployment LoginWebApp #minReadySeconds: 15
microk8s.kubectl set image deployment LoginWebApp LoginWebApp=myloginapp:mycicd-0.0.2.RELEASE

microk8s.kubectl describe pod LoginWebApp-58ff5dd898-9trh2

```

Like wise can be used on any cloud infrastructure thru api's such as gcloud, etc.