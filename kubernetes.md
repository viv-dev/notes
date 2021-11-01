# kubernetes

## Glossary

### Cluster

This is a collection of nodes - each able to spin up multiple instances of the pods that make up an application. They share services, so an instance of a pod can be accessed by any other pod on any other node in the cluster.

### Node

Largest unit in a Kubernetes cluster. Can be a physical machine, virtual machine, or even a container.

### Pod

Smallest unit of kubernetes. It is an abstraction layer over the top of the containerisation platform. Multiple containers in theory can run in a pod, but it's best practice to limit it to one container per pod.

Each pod gets it's own IP address - however they are designed to be 'ephemeral', so if the application inside crashes, Kubernetes will create a new instance of the pod... with a new IP address. Pods live in an internal virtual network.

### Service

A service is a static IP address that is attached to each pod. The lifecycle of the service and the pod are not connected, so if the pod dies the service will remain and the new pod can be attached to the same service. As such, services are the primary method for exposing communication to a pod, as a service

In a cluster, the service will also map pods accross nodes and act as a load balancer, directing a request from one pod to the most suitable pod instance currently running.

### Ingress

Since the pods live inside their own virtual network, if you want to access a service inside a deployment, you will need an Ingress service.

Ingress maps a nice external domain/port combination to an internal service.

### ConfigMap 

The purpose of this is to handle configuration for a set of pods - e.g. a front end application needs to be configured with the URL the database.

This is what you use to wire pods together through the services.

Not intended to hold sensitive data like usernames/passwords/tokens etc!

### Secret

Like above, but designed to hold sensitive configuration data. Stores all values in base64 encoded format (wait, really?).

> NOTE: Both Secrets and ConfigMap can be exposed to the app in a pod by either environment variables or a properties file.

### Volumes

Volumes are used to manage persistent data that needs to survive a pod dying and being reincarnated.

Storage for volumes can be a local hard drive, or even a remote network storage solution (depends on the deployment environment).

### StatefulSet 

When running pods across multiple nodes in a cluster, in order to share the same state they will need to read from the same persistent storage volume. There therefore needs to be some arbiter who decides which pod can be accessing a particular part of a volume at any time.

This is only required for applications/pods that require such persistent data - such as databases.

However, statefulSet is apparantley a pain to set up - herefore databases are often hosted outside of a Kubernetes cluster for this reason.

### Deployment

A yaml file that describes a setup of all of the above - this is what defines what applications run in pods, how many instances of each pods there will be, etc.

## Architecture

Each node in a cluster must have the following services installed:

- **Container Runtime**: most often Docker, but kubernetes supports many container types
- **Kublet**: The kubernetes runtime itself - manages/orchestrates the containers
- **Kube Proxy**: Manages forwarding information between nodes in a cluster

In a cluster, there must also be at least one node that is designated the 'master node' (there can, and should be, multiple master nodes for redundancy however). This master node is responsible for deciding where and how in the cluster pods will be run/scheduled.

Master nodes run 4 additional processes:

- **Api Server**: this is what lets a client interact 'cluster' as a single end point. It can recieve commands via an api, or the official kubernetes cli, `kubectl`. It also acts as a gate keeper for authentication.
- **Scheduler**: When you send a request to schedule a pod to the `api server`, it will forward that request on to the scheduler who will decide which node/nodes will run the pods in a deployment.
- **Controller Manager**: responsible for detecting when a pod dies and invoking the scheduler to re-create a dead pod.
- **etcd**: a key value store of the cluster state - aka, the cluster brain. Nodes report their resource utilisation and state to etcd, and the scheduler/controller manager use information from this store in order to respond to/action events.

In a multi-master server, the `Api Server` is load balanced between the two masters, and `etcd` is a distributed storage.

## Creating a Deployment

A `deployment` manages a `replicaset` which is a description of how to manage a `pod` which is an abstraction over a `container`.

Anything below a `deployment` should be managed by kubernetes, and so our focus should be at this level.

You create a deployment for each seperate pod/image (unlike docker compose where you can declare multiple containers in one file). To create a deployment, you can use the following command:

`kubectl create deployment <deployment-name> --image=<image-name>`


To edit an already deployed deployment:

`kubectl edit deployment <deployment-name>`

If you want to edit a deployment 'in bulk' you can apply deployment parameters from a kubernetes yaml file:

`kubectl apply -f <file-name>.yaml`

You can use this file to manage the deployment going forward, including deleting the deployment:

`kubectl delete -f <file-name>.yaml`

You can get an existing deployment (stored in the `etcd` service) with the following command:

`kubectl get deployment nginx-deployment -o yaml`

## Pods

Pod name consists of the following structure:

`<deployment-name>-<replica-set-id>-<pod-id>

To get information about the state of a specific pod you can use:

`kubectl describe pod <pod-name>`

To see the replicasets:

`kubectl get replicaset`

To inspect the log output of a running pod you can use the following command:

`kubectl logs <pod-name>`

## Service

## Secret

In order to reference a secret in a deployment configuration file, the secret must be created before hand. 

From a kubernetes secret file, you can import it into your kubernetes environment by running:

`kubectl apply -f <secret-file-name.yaml>`

## Debugging

To get into a pod:

`kubectl exec -it <pod-name> bin/bash`

To follow the pod logs:

`kubectl logs --follow <pod-name>`

## Taints

A taint is a flag placed on a node to reject a certain deployment from running on it.

Tolerations override a taint to allow it to run on a tainted node.

## Commands

### Minkube

```bash
# Create cluster
minikube start --vm-driver=<vm-driver>
kubectl get nodes
minikube status
kubectl version

# Delete cluster and restart in debug mode
minikube delete
minikube start --vm-driver=<vm-driver> --v=7 --alsologtostderr
minikube status
```

### Kubectl

```bash
# Kubectl Commands
kubectl get nodes
kubectl get pod
kubectl get services
kubectl create deployment {deployment-name} --image={image-name}
kubectl get deployment
kubectl get replicaset
kubectl edit deployment {deployment-name}

# Kubectl debugging
kubectl logs {pod-name}
kubectl exec -it {pod-name} -- bin/bash

# Create mongo deployment
kubectl create deployment mongo-depl --image=mongo
kubectl logs mongo-depl-{pod-name}
kubectl describe pod mongo-depl-{pod-name}

# Delete deployment
kubectl delete deployment mongo-depl
kubectl delete deployment nginx-depl

```