---
title: "Recipe #2. Learn DevOps Part 1"
date: 2017-10-16T06:46:07+02:00
draft: false
---
<br>


# Setup

### 1. How to setup Vagrant Box


# Usage

### Open Linux Box on virtual machine
```
$ cd
$ cd ubuntu
$ vagrant up
$ vagrant ssh
```

### Deleting Instances and Volumes

(Starting from ssh-ing to the account from VM Linux Machine)

```
$ kops delete cluster --name kubernetes.newtech.academy --state=s3://kops-state-b429b # names are placeholders
# take a look at a list of clusters you will be deleting
$ kops delete cluster --name kubernetes.newtech.academy --state=s3://kops-state-b429b # names are placeholders --yes 

```

### Dockerizing




# Section 1, Lection 14

In Vagrant Box,

### Install Docker

```
$ sudo apt-get install docker.io
$ docker -v
```
<br>

### Install Git

```
$ sudo apt-get install git
```

so that I can use `git clone`
<br>

### Docker build

Build a container following commands from `dockerfile`

```
$ docker build .
```
<br>

### Docker Run
```
$ docker run -p 3000:3000 -it [docker id]
```

Run [docker id] container **getting** from port 3000 to **exposing** to port 3000 of the host system.

Note: Left 3000 is where this app is running on. 
And we want to expose it as 3000 (right 3000) on the host system. In tutorial, 'ubuntu-xenial' is the host system.

##### How to Check

open another window and command `curl localhost:3000` will display whatever the app is supposed to display.


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


