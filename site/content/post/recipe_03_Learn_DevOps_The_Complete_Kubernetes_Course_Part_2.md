---
title: "Recipe #3. Learn DevOps Part 3"
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
