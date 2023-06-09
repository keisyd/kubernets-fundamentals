# Overview

Hello folks. Let's talk about this amazing
The amaziness of Kubernetes starts with the fact that it holds several years of experience from Google Temmantes and the Open Source community since 2014, altoghether to create this platform for managing containerized workloads and services.

With the rize of cloud native alongside with the popularity of containerized solutions and micro-service based applications, the tech community strived for a concise, extensible and powerfull solution for managing so many services.

For those who are infamiliar with containers, let's take a closer look.

Remmeber, back in the time when computers were single purposed and after that we got to OS's to give it more power? As long as we didn't need the all in one machine to scale rapidly it was the best we could have for future prof infrastructure.

Altough, as we grew in connectivity, we also grew the need for fast response out of the things on the internet. So, virtualization comes in as an alternative to bare hardware mainence everytime a company needed to scale.

But the all in one problem would remain, because while making a all in one solution virtual might enable heavy weight hardware to stand out for longer before it rans out capacity for every new Virtual Machine, we are still replicating and copying the whole thick ass future prof Operational System that holds, mostly, a lot more capability than your service actually needs.

That's when ships transportation system for managing loads comes as an inspiration: and YES I'm talking about containers

## Leasons from containers

An efficient ship transportation ship that is the combination of bringing the load, loading the ship, transporting and unloading it in a different location, wouldn't stand or scale in size properly if you had varied shape loads and cargo. That's why they choge the most space efficiente 3 dimentional shape to define the boundries of each cargo so the ship loaders would never worry about what's inside, and a macanism of loading ships of any size would be possible by stacking containers.

In kubernetes, a really important aspect of it's archtectural solution is it's isolation, where you only work with the kubernetes inrterfaces. what this solution brings to us is a comprehensive and extensible way to provite

- High acailabity or no downtime
- Scalability or high performance
  and Disaster recovery ( backup and restore)

# Architecture

For node we should think about a computer or VM

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
