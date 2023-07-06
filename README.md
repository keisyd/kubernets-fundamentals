# Kubernetes

# A first look

Kubernetes organizes itself in a particular way to make container deployment possible, scalable, predictable, declarative and, generally, customizable. 

There’s no such thing as a container for Kubernetes, so it abstracts way any container implementation using a structured and reliable interface called CRI or Container runtime interface. In fact, that’s one of the three key pillars and plugins for Kubernetes core functionality to make it work as intended. 

Also, in order the compute, launch and have control over container deployment, Kubernetes provides the smallest active entity it controls which is the Pod.

A Pod contains from one to several containers mostly single purposed and is basically the Kubernetes unit of control. They are created inside a running machine that’s is called Node which is responsible for providing the computational power for the pods to operate. 

Kubernetes works in a declarative way, so we never create Pods directly - even though possible, because we want it to be automated. The way this automation works is that we tell to Kubernetes the desired state of the cluster via one of the Workload documents, such as the Deployment, so Kubernetes tries it’s best to achieve the desired state.

So instead of saying where each Pod goes or in what nodes, we say we want a given number of replicas - that is later translated to Replica Sets, for example, an the Kubernetes fights against the Pods ephemeral nature each time a Pod Fails to keep the specified state.

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
- You can tell kube-proxy which one to use and the **************default************** is IPTables

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

Services are a resource Kind, in the ************Core************ group at maturity ****v1****. Like all resources they require a name  in the metadata. So, if the pod matches the selector it get’s the connection from a client that connects to the service virtual ip.

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