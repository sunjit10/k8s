# Kubernetes Concepts

- Kubernetes coordinates a highly available cluster of computers that are connected to work as a single unit. The abstractions in Kubernetes allow you to deploy containerized applications to a cluster without tying them specifically to individual machines.
- To make use of this new model of deployment, applications need to be packaged in a way that decouples them from individual hosts: they need to be containerized.
- Containerized applications are more flexible and available than in past deployment models, where applications were installed directly onto specific machines as packages deeply integrated into the host. Kubernetes automates the distribution and scheduling of application containers across a cluster in a more efficient way.
- Kubernetes is an open-source platform and is production-ready.


A Kubernetes cluster consists of two types of resources:

- The Control Plane coordinates the cluster
- Nodes are the workers that run applications

## Control Plane

- The Control Plane is responsible for managing the cluster. The Control Plane coordinates all activities in your cluster, such as scheduling applications, maintaining applications desired state, scaling applications, and rolling out new updates.
- When you deploy applications on Kubernetes, you tell the control plane to start the application containers. The control plane schedules the containers to run on the cluster's nodes.


## Deployment

- The Deployment instructs Kubernetes how to create and update instances of your application.
- Once you've created a Deployment, the Kubernetes control plane schedules the application instances included in that Deployment to run on individual Nodes in the cluster.

## Deployment Controller

- Once the application instances are created, a Kubernetes Deployment controller continuously monitors those instances.
- If the Node hosting an instance goes down or is deleted, the Deployment controller replaces the instance with an instance on another Node in the cluster. This provides a self-healing mechanism to address machine failure or maintenance.

## Node

- A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster.

- Each Node is managed by the control plane. A Node can have multiple pods, and the Kubernetes control plane automatically handles scheduling the pods across the Nodes in the cluster. The control plane's automatic scheduling takes into account the available resources on each Node.

Every Kubernetes Node runs at least:

- Kubelet, a process responsible for communication between the Kubernetes control plane and the Node; it manages the Pods and the containers running on a machine.
- A container runtime (like Docker) responsible for pulling the container image from a registry, unpacking the container, and running the application.

## Kubelet

- Each node has a Kubelet, which is an agent for managing the node and communicating with the Kubernetes control plane.
- Node-level components, such as the kubelet, communicate with the control plane using the Kubernetes API, which the control plane exposes.
- End users can also use the Kubernetes API directly to interact with the cluster.

## Pods

A Pod is a Kubernetes abstraction that represents a group of one or more application containers (such as Docker), and some shared resources for those containers. Those resources include:

- Shared storage, as Volumes
- Networking, as a unique cluster IP address
- Information about how to run each container, such as the container image version or specific ports to use

- Containers should only be scheduled together in a single Pod if they are tightly coupled and need to share resources such as disk.

- Pods that are running inside Kubernetes are running on a private, isolated network. By default they are visible from other pods and services within the same Kubernetes cluster, but not outside that network.


## Proxy

The kubectl proxy command can create a proxy that will forward communications into the cluster-wide, private network. The proxy can be terminated by pressing control-C and won't show any output while it's running.

You can see all those APIs hosted through the proxy endpoint. For example, we can query the version directly through the API using the curl command:

curl http://localhost:8001/version

In order for the new Deployment to be accessible without using the proxy, a Service is required

The API server will automatically create an endpoint for each pod, based on the pod name, that is also accessible through the proxy.

First we need to get the Pod name, and we'll store it in the environment variable POD_NAME.
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME

You can access the Pod through the proxied API, by running:
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME:8080/proxy/

