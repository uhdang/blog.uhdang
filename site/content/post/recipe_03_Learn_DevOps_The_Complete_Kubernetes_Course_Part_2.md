---
title: "Recipe #3. Learn DevOps Part 2"
date: 2017-10-16T06:46:07+02:00
draft: false
---


# Kubernetes Basics

### Section 2, Lecture 22 - Node Architecture
<br>

![Node_Architecture](/images/node_architecture.png)

### Section 2, LEcture 23 - Replication Controller (Scaling Pods)
<br>

- Scaling in Kubernetes can be done using the _Replication Controller_.
- The replication controller will _ensure_ a specified number of _pod replicas_ will run at all time.
- A pod created with the replica controller will _automatically_ be repliaced if they fail, get deleted, or are terminated.
- If you want to make sure a pod running always, _replication controller_ is recommended.

Creating an example app 2 times with Replication Controller

![Replication_Controller](/images/example_creating_pod_with_replication_controller.png)

### Section 2, Lecture 24 - Demo: Replication Controller - Horizontally scale a pod with the replication controller
<br>

Using a command targetting a proper `.yml` will create pods

With yml file above, using the command below will create two pods
```
# Build pods outlined in yml file
$ kubectl create -f kubernete-course/replication-controller/helloworld-repl-controller.yml

# Check created pods
$ kubectl get pods

# Check individual pod
$ kubectl describe pod [pod name]

# Delete a pod
$ kubectl delete pod [pod name]
```

If you try deleting one of the nodes, you will see kubenertes automatically creating a node again.
Controller will always make sure correct number of pods are running.

Scale using command
```
# scale pod using yml file
$ kubectl scale --replicas=4 -f [kubernetes-course/replication-controller/helloworld-repl-controller.yml]

# check replication controllers
$ kubectl get rc

# scale using replication contoller command (scale it to 1)
$ kubectl scale --replicas=1 rc/helloworld-controller

# delete replication controller
$ kubectl delete rc/helloworld-controller
```

Note: This will only work on 'stateless' pods

![Scaling_Replication_Controller](/images/scaling_replication_controller.png)


### Section 2, Lecture 25 - Deployments
<br>

##### Replication Set

_Replica Set_ is the next-generation Replication Controller. It is used by the Deployment object.
<br>

##### Deployments

With a deployment object you can:
- _Create_ a deployment (i.e. deplyoing an app)
- _Update_ a deployment (i.e. deplyoing a new version)
- Do _rolling updates_ (zero downtime deployments)
- _Roll back_ to a previous version
- _Pause / Resume_ a deployment (i.e. to roll-out to only a certain percentage)
<br>

##### Useful Commands

![deployment_useful_commands](/images/deployment_useful_commands.png)

### Section 2, Lecture 26 - Demo: Deployment
<br>
demo video shows command usages well
<br>

### Section 2, Lecture 27 - Services

- _Pods_ are very _dynamic_, they come and go on the Kubernetes cluster.
  - When using a _Replication Controller_, pods are _terminated_ and created during scaling operations.
  - When using _Deployments_, when _updating_ the image version, pods are _terminated_ and new pods take the place of older pods.
- That's why Pods should never be accessed directly but always through a _Service_.
- A service is the _logical bridge_ between the "mortal" pods and other _services_ or _end-users_.
- When using the "kubectl expose" command earlier, you created a new Service for your pod, so it could be accessed externally.
- Creating a service will create an endpoint for your pod(s):
  - a _ClusterIP_: a virtual IP address only reachable from within the cluster (this is the default)
  - a _NodePort_: a port that is the same on each node that is also reachable externally.
  - a _LoadBalancer_: a LoadBalancer created by the _cloud provider_ that will route external traffic to every node on the NodePort (ELB on AWS)
- The options above only allow you to create _virtual IPs_ or _ports_
- There is alos a possibility to use _DNS names_
- Service Example:

![service_example](/images/service_example.png)

### Section 2, Lecture 28 - Demo: Service

```
# Node check
$ kubectl get node

# Pod check
$ kubectl get pods

# in case pods need to be made, here is the command to create pod
$ kubectl create -f first-app/helloworld.yml

# describe the pod
$ kubectl describe pod [pod name]
```

after pods are running, lets run service

```
$ kubectl create -f first-app/helloworld-nodeport-service.yml
```

In AWS, by goint to 'security group' menu, you can check url and check if service is running.

```
# command to describe service
$ kubectl describe svc helloworld-service

# get summary of all running services
$ kubectl get svc

# delete the service
$ kubectl delete svc [service name]
```

### Section 2, Lecture 29 - Labels

### Section 2, Lecture 30 - Demo: Labels

### Section 2, Lecture 31 - Health Checks

- if your application _malfunctions_, the pod and container can still be running, but the application might not work anymore.
- To _detect_ and _resolve_ problems with your application, you can run _health checks_.
- You can run 2 different types of health checks
  - Running a _command_ in the container _periodically_.
  - Periodic checks on a _URL_(HTTP)
- The typical production application behind a load balancer should always have _health checks_ implemented in some way to ensure _availability_ and _resiliency_ of the app.

![health_check](/images/healthcheck_example.png)

### Section 2, Lecture 32 - Demo: Health checks

### Section 2, Lecture 33 - Secrets

### Section 2, Lecture 34 - Demo: Credentials using Volumes

### Section 2, Lecture 35 - Demo: Running Wordpress on Kubenernetes

### Section 2, Lecture 36 - WebUI

### Section 2, Lecture 37 - Demo: WebUI