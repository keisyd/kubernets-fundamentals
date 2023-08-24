# Overview

Hello folks. Let's talk about the amaziness of Kubernetes starts with the fact that it holds several years of experience from Google Temmantes and the Open Source community since 2014, altoghether to create this platform for managing containerized workloads and services.

With the rize of cloud native alongside with the popularity of containerized solutions and micro-service based applications, the tech community strived for a concise, extensible and powerfull solution for managing so many services.

For those who are unfamiliar with containers, let's take a closer look.

Remmeber, back in the time when computers were single purposed and after that we got to OS's to give it more power? As long as we didn't need the all in one machine to scale rapidly it was the best we could have for future prof infrastructure.

Altough, as we grew in connectivity, we also grew the need for fast response out of the things on the internet. So, virtualization comes in as an alternative to bare hardware mainence everytime a company needed to scale.

But the all in one problem would remain, because while making a all in one solution virtual might enable heavy weight hardware to stand out for longer before it rans out capacity for every new Virtual Machine, we are still replicating and copying the whole thick ass future prof Operational System that holds, mostly, a lot more capability than your service actually needs.

That's when ships transportation system for managing loads comes as an inspiration: and YES I'm talking about containers

## Leasons from containers

An efficient ship transportation system that is the combination of bringing the load, loading the ship, transporting and unloading it in a different location, wouldn't stand or scale in size properly if you had varied shape loads and cargo. That's why they choose the most space efficiente 3 dimentional shape to define the boundries of each cargo so the ship loaders would never worry about what's inside, and a macanism of loading ships of any size would be possible by stacking containers.

In kubernetes, a really important aspect of it's archtectural solution is it's isolation, where you only work with the kubernetes interfaces. What this solution brings to us is a comprehensive and extensible way to provite

- High availability or no downtime
- Scalability or high performance
  and Disaster recovery ( backup and restore)

# Architecture

For node we should think as a computer or a VM.

There's the **master** node (primary "node agent") and connected to it there're the **worker nodes** each one with a kubelet process running on it, which is a client that enable connectivity between the nodes on the clusters and the master node among other things.

Each worker node have containers of different applications deployed on it where, so the number of containers on the worker node may vary depending on the application.

## Master node (Control Plan

The master node contains several components necessaries for Kubernetes to function properly:

### Api Server

Is the entry point to the kubernetes cluster, so this is the process which the different kubernetes clients will talk to: ui, scripts and command line tool.

### Controller Manager

Which keeps track of whats happening in the cluster, wheter something went wrong, a container died, etc

### Scheduler

Which decides on which node a new pode should be scheduled based on the Replica Set conffiguration and the state of each node pod.

### etcd

The key-value storeg that holds at any time the current state of the kubernetes cluster like the status and data configuration for each node, which can be recovered thanks to a snapshot based storage.

## Virtual network

The kubernetes infrastructure enables network like connectivity between the nodes enabling the function of the components and things like load balacing thought an IP based network. Also, because of this the nodes are able to talk to each other.

The worker nodes tend to have more resources but the master nodes have to be replicated for fault tolerance and cluster availability.

# Main components

## Pods

Pod is the smallest unit in the Kubernetes and server as an abstraction over a container to separetes it from the runtime and container technology. So you only interact with the kubernetes layer.

The pod, much like the container itself, is a single purpose unit, so the only cases when it runs more than one container is when you have helper or side services to run as an external dependency of this container. In general, most pods will contain only one container.

Each pode contain it's own IP address. Pods are ephemeral and can die easily and a new one will be replaced and re-assinged. So, for communication purposes, we use services.

## Services

Service is basically a static IP address tha can be attach to each pod, which is necessary for keeping the access to a given pod even when it fails and dies.

## External Services vs Internal Serivce

If it's the case for you to want to connect your application with the internet, you may use an external service that enables internet connectivity from a given service pod. But services that will be avaible only within the kubernetes network have to be internal ones. But none of those connection are production ready or user friendly.

## Ingress

Is the end point router that recieves from the internet the requests and than call the services.

## Config Map

Basically the way to place environment variables. Not ideal for sensitivy data.

## Secret

Used to store secrets. Although they are not secured out of the box, they are meant to be encripted by 3th parth solution like Vauths

## Volume

Data storage is a comon problem because containers are usually stateless. So, the way they are managed by a kubernetes cluster is not ideal, so in order to have persistent data, you need to use Volumes. Basically it attaches a physical storage outside the kubernetes storage, so every time the container of your database is restarted it pull the data from the outside with the volume.

## Deployments

We do not interact with pod directly, instead we tell how we want the service ( that also works as a load balancer ) how many of the pods are going to b exepcted and other configuration throught a configuration Blueprint call Deployment that tell how many replicas of the pod is expected for the cluster and how the service will react to the state of the cluster.

## Statefulset

So, if deployments can manage stateless well, data inconsitency that happens when two pods are doing concurrent operations on a database, can only be corrected by a stateful set. Which is a uncomon practice due to it's complexity.

# A first look

Kubernetes organizes itself in a particular way to make container deployment possible, scalable, predictable, declarative and, generally, customizable.

There’s no such thing as a container for Kubernetes, so it abstracts away any container implementation using a structured and reliable interface called CRI or Container runtime interface. In fact, that’s one of the three key pillars and plugins for Kubernetes core functionality to make it work as intended.

Also, in order the compute, launch and have control over container deployment, Kubernetes provides the smallest active entity it controls which is the Pod.

A Pod contains from one to several containers mostly single purposed and is basically the Kubernetes unit of control. They are created inside a running machine that’s is called Node which is responsible for providing the computational power for the pods to operate.

Kubernetes works in a declarative way, so we never create Pods directly - even though possible, because we want it to be automated. The way this automation works is that we tell to Kubernetes the desired state of the cluster via one of the Manifest documents, such as the Deployment, so Kubernetes tries it’s best to achieve the desired state.

So instead of saying where each Pod goes or in what nodes, we say we want a given number of replicas - that is later translated to Replica Sets, for example, an the Kubernetes flights against the Pods ephemeral nature each time a Pod Fails to keep the specified state.

Here we got to another important factor: Kubernetes is initially designed for ephemeral and stateless services that can potentially fail without compromising the cluster health if more of them can be provided on time.

So, we do not expect, in general, those pods to last long, instead, we worry about having a great number of them to handle the current load.

# Scratching the surface of the network

Even though ephemeral, Kubernetes don’t want pods to be isolated. So it provides enough resources to make any pod talk to another no matter it’s location within the cluster.

Well, it’s easy to reason about the communication of Pods within a Node because, as we know, the Linux implementation of a switch for local containerized networks called Bridge already enables that. Although, Pods should be machine agnostic by design, so that’s exactly what Kubernetes manages to provide with CNI.

But, first, what is a SDN? Stands for Software defined networking, which are software based solutions that enables a network like system to take place. For the Pods case, it should enable Pods to talk to each other.

But following the tradition, each abstraction need for Kubernetes is implemented through a standard and, in this case, it’s the CNI, Container Networking Interface.

So, the CNI solutions have at least one but generals two components

- CNI plugins provide:
  - Pod network wiring
  - Pod IP addresses
  - Network policy implementation
  - More

## CNI components

They will have a Daemon Set component that runs in every node in the cluster and handles the organization of all the pods in that cluster. Sometimes there’s a control plane too.

It’s important to understand the place of choice also for this component of Kubernetes.

# Services

Going back to the ephemeral nature of Pods, we got to understand that software basically runs for clients. So the cluster clients should not handle the dynamic nature of your cluster, so, to eliminate that problem we place a service in front of your pods.

Services are basically an abstraction for an IP and table resolution that enables you to access multiple pods of the same service from one entry point.

# Cluster IP implementation

Kubernetes then provides this and one of it’s form is the [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) that runs as a DaemonSet in within each pod and do just that: every time service is created it acknowledges it’s creation and enables the communication through those services. As the pods come and go, kube-proxy takes care of writting and erasing them from the mesh.

It can do this in some ways:

- User mode (Slow and deprecated)
- IPTables
  - Here we just tell the kernel “hey, when some of the pods connects to this IP address use a Destination Network Address Translation to one of these Pods”. Also it does random selection of the pods hence it’s Load Balancing capabilities. Even when a Pod gets evicted, the load is automatically re-distributed. In anyway, from a client perspective, it’s important to understand the ephemeral nature of those connections: if anything goes wrong, reconnect!
- IPVS
  - One possible evolution from IPTables is this new kernel mechanism called Ip virtual Services that uses less resources and makes it’s sometimes significantly faster than IP Tables
- You can tell kube-proxy which one to use and the **\*\***\*\***\*\***default**\*\***\*\***\*\*** is IPTables

There also CNI plugins with some fancy features. Some of the plugins allow you to replace kube-proxy.

### Independent Address Spaces

All the hosts are assigned to unique addresses to enable their communication. Also the Pods have to have not overlapping addresses and that’s the same for the services. So, we have three not overlapping Ip Spaces:

- HostIP (node) Range - managed by IT or your Cloud
- ClusterIp Range - configured when installing K8s
- PodIP Range - configured when installing your CNI plugin

## Specifying a Service

The way it works is that we declare a state and Kubernetes tries to make the status of the cluster the one declared on the yaml.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  ports:
  - port: 80
      name: http
  selector:
      app: web
```

Services are a resource Kind, in the **Core** group at maturity **v1**. Like all resources they require a name in the metadata. So, if the pod matches the selector it get’s the connection from a client that connects to the service virtual ip.

The way it happens is that the Service Controller watches an incoming pod and creates an EndPoint resource that is watched by the kube-proxy to redirect the requests.

There are some types of services:

- Headless (special case of CluterIp)
  - It’s a Service of ClusterIp type with no cluster IP. It’s good for situations when your pods are not micro-services. Such as when you have a pod for each kafka topic, you use Headless
  - Supports name resolution but not forwarding
- ClusterIp
  - Cluster IP is for micro-services when you have an Ip on the cluster that load balances the requests to your pods
- NodePort
  - For external service access via a universal port
- LoadBalancer
  - Use a plugin to enable an external load balancer
- ExternalName
  - Aliases this service to the specified externalName
