---
title: "Recipe #2. Learn DevOps Part 1 - Kubernetes Setup"
date: 2017-10-25T06:46:07+02:00
draft: false
---
<br>

# General Workflow (Rough)

VM setup => Connect it to AWS => Run Kubernetes Cluster => Run Image on created Node => Docker setup + run => upload as Docker registry => Create Kubernetes Pod => ... => Run app behind a load balancer


# Cloud Setup

### Virtual Machine Setup and Usage - Vagrant

Setup
 
```
$ mkdir ubuntu
$ cd ubuntu
$ vagrant init ubuntu/xenial64
$ vagrant up

$ vagrant ssh-config
$ vagrant ssh
```

Open Linux Box on virtual machine

```
$ cd
$ cd ubuntu
$ vagrant up
$ vagrant ssh
```

### Kops (Kubernetes Operations) setup in VM

```
$ wget https://github.com/kubernetes/kops/releases/tag/1.7.1/kops-linux-amd64   # download the latest kops
$ chmod +x kops-linux-amd64                                                     # Add execution permissions
$ sudo mv kops-linux-amd64 /usr/local/bin/kops                                  # Move the kops to /usr/local/bin

$ sudo mv /usr/local/bin/kops-linux-amd64 /usr/local/bin/kops                   # rename kops binary to kops instead of kops-linux-amd64
                                                                                # use if necessary 
```

### Setup AWS CLI in VM

```
$ sudo apt-get install python-pip       # necessary to download AWS
$ sudo pip install awscli               # install aws commandline tool
```

In order to use AWS from the command line, you need to make connection. For this, you first need to add a user at `Identity & Access Management` section of AWS Dashboard. Create a user and generate an **access key** that you will need to configure AWS on your VM.

After creating a user, configure AWS in your VM shell.

```
$ aws congfigure    # Input private key + secret private key.
                    # No need for region and output format

$ ls -ahl ~/.aws/   # will give `config` and `credentials` folder as proof of configuration.
```

Make sure to go to `user` option in `Identity & Access Management`, and _Attach Policy_ to give **AdministratorAccess**


### Create S3 Bucket for Kops State

![create_s3_bucket](/images/create_s3_bucket.png)

Go to S3 section in AWS dashboard and create a S3 bucket for Kops state. Make sure to give a name with a *random string* at the end, for this name has to be unique in the Region. Region you specify here will be where your cluster will be created.

Tip. www.cloudping.info provides Latency from where you are.


### DNS Setup

Go to Route53 and create a hosted zone. Whatever name it gives, it will have the values that needs to be connected to Domain. (Domain -> Value)

Note. How you connect Value to the domain depends on where you get the Domain.


# Cluster setup using AWS Cli

Install kubectl
```
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.6.1/bin/darwin/amd64/kubectl
$ sudo mv kubectl /usr/local/bin/           # move it to bin
$ sudo chmod -x /usr/local/bin/kubectl      # give execution permission
$ kubectl                                   # check kubectl availability
```

Create `ssh key` to log-in to the cluster

```
$ ssh-keygetn -f .ssh/id_rsa        # generate ssh-key to .ssh/id_rsa 
```

Create cluster
```
$ kops create cluster --name=kubernetes.newtech.academy --state=s3://kops-state-b429b --zones=eu-west-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.newtech.academy       # this will give you a preview before creating cluster
```

Command Analysis

- --name : full DNS name
- --state : save state in stated s3 bucket
- --zone : zone where cluster will be created
- --node-count : node count
- --node-size : node size
- --master-size : master node size
- --dns-zone : DNS name

This command will create one master (t2.micro) and two nodes (t2.micro) 

```
$ kops update cluster kubernetes.newtech.academy --yes      # once ready input this command given from a preview
```

Note. There are multiple suggestions from preview for editing before launching as well as launching command.

![cluster_launch_review](/images/cluster_launch_review.png)

You can check _username_ and _password_ kubernetes has configured for us in a certificate at `config` file, of which path is displayed at the end of cluster creation. You can 'cat' it - `cat ~/.kube/config`.
<br>

### Workflow

VM setup => Connect it to AWS => Run Kubernetes Cluster(**this is where we are**) => Run Image on created Node

# Useful Kubernetes Commands

Check Node

```
$ kubectl get node      # can check which nodes are running
```

Check Service

```
$ kubectl get service       # you can check deployment, port, and others.
```

Run a service on node

```
$ kubectl run hello-minikube --image=gcr.io/google_container/echoserver:1.4 --port=8080         # run minikube on node
```

Note. Typically when you want to access a service you put ELB in front. But you can still expose the port without it with below command.

```
$ kubectl expose deployment hello-minikube --type=NodePort      # expose deployment
$ kubectl get service                                           # need to check exposed port and open it in order to access it directly without ELB
```

After checking an exposed port, go to Security Groups in VPC management section from AWS dashboard, access _Inbound Rules_ of master node, and specify `Port Range` as exposed port. 

![expose_service_port](/images/expose_service_port.png)

*You can check a created route from Route 53 (i.e. api.kubernetes.newtech.academy) and try accessing it in url as well.

Deleting Instances and Volumes

```
$ kops delete cluster --name kubernetes.newtech.academy --state=s3://kops-state-b429b # names are placeholders
# take a look at a list of clusters you will be deleting
$ kops delete cluster --name kubernetes.newtech.academy --state=s3://kops-state-b429b # names are placeholders --yes 

```

<br>
# Sorted up till here


# Dockerizing

Install Docker

```
$ sudo apt-get install docker.io
$ docker -v
```

Install Git

```
$ sudo apt-get install git          # so that I can use `git clone`
```

Docker build

```
$ docker build .            # Build a container following commands from `dockerfile`
```

Docker Run

```
$ docker run -p 3000:3000 -it [docker id]
```

Note. Run [docker id] container **getting** from port 3000 to **exposing** to port 3000 of the host system.

Note: Left 3000 is where this app is running on. 
And we want to expose it as 3000 (right 3000) on the host system. In tutorial, 'ubuntu-xenial' is the host system.

**How to Check**

open another window and command `curl localhost:3000` will display whatever the app is supposed to display.




--- Up till here Line ---


# Section 1, Lecture 15 - Docker Registry

To make an image available to Kubernetes, you need to push the image to a Docker Registry, like Docker Hub.

### Docker Remarks:
- Limitations:
  - You should only run _one process in one container_
  - All the data in the _container_ is _not preserved_, when a container stops, all the changes within a container are lost
  
- A few official images you might use for your app:
  - https://hub.docker.com/_/nginx/ - webserver
  - https://hub.docker.com/_/php/ - PHP
  - https://hub.docker.com/_/node/ - NodeJS
  - https://hub.docker.com/_/ruby/ - Ruby
  - https://hub.docker.com/_/python/ - Python
  - https://hub.docker.com/_/openjdk/ - Java
<br> 

# Section 1, Lecture 16 - Docker Registry Demo

Login to dockerhub
```
$ docker login
```

### Tag an image
```
$ docker tag [docker image ID] [docker respository name]    #docker respository composes of [useID]/[name]
```
<br>

# Section 1, Lecture 17 - Running first app

Before we can launch a container based on the image, we need to create a _pod definition_

- _A pod_ describes an application running on Kubernetes
- A pod can contain _one or more tightly coupled containers_, that make up the app. 
Those apps can easily communicate with each other using their local _port numbers_

### Useful Commands

`kubectl get pod` : Get information about all running pods
`kubectl describe pod [pod]` : Describe one pod
... more

# Section 1, Lection 18 - Running first app Demo
# Section 1, Lecture 19 - Useful kubectl commands

# Section 1, Lecture 20 - Service with LoadBalancer

- In a real world scenario, you need to be able to access the app from _outside_ the cluster.
- On AWS, you can easily add an _external Load Balancer_.
- This AWS Load Balancer will route the traffic to the correct pod in Kubernetes
- There are other solutions for other cloud providers that don't have a Load Balancer
  - Your own _haproxy / nginx_ load balancer in front of your cluster.
  - Expose ports directly

# Section 1, Lecture 21 - Running the first app behind a load balancer

create a pod
```
$ kubectl create -f first-app/helloworld.yml
$ kubectl create -f first-app/helloworld-service.yml
```

### Route
 
1. Created Record Set from Route 52 (helloworld.kubernetes.jeong.world)
2. Alias Targetting to created Loadbalancer
3. Loadbalancer redirects traffic from port 80 to one of created nodes (instances) on Instance Port (i.e. 32225)
4. Traffic redirected to the pod


