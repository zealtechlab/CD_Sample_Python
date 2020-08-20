# CD_Sample_Python

This Repo demonstrates Containerisation and automated infrastructure provisionings for the Continuous Deliver/Deployment. Include Ansible Play book based deployment.

**Objective**: Write and run an Ansible playbook which will install Docker, Minikube and kubectl and then create a deployment in minikube with an app running.

## Dockerize the app

A simple dockerfile and docker-compose.yml to deploy the war app

## minikube the app deploy

Install minkube

```bash
snap install minikube
```

Start a minikube cluster [May take few minutes to start kubernetes cluster]

```bash
minikube start
```

Refer kube folder

## Ansible playbook

```bash
ansible --version

ansible-playbook ansible/playbook.yml 
```

For Jenkins to be able to connect to the kube host and deploy the app, copy the kube config file from the kube host machine to the data folder of Jenkins. There are more than one way to do this. Another way is to ssh to the kube cluster master machine, issue config view command to gather the kube configs and proceed.

For ssh connect to work
```cp docker_devops_stack/jenkins/config/ssh-keys/cd-demo.pub ~/.ssh/```
