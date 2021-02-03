# K8s-Learning-Notes
My personal notes for learning Kubernetes (K8s).

# K8s Components
## Pods
1. **Pod(s)** are the smallest unit of K8s. A **Pod** (a pod of whales or a pea pod) is a group of one or more containers that share storage, network resources, and configuration for how to deploy themselves.
2. In terms of **Docker** concepts, a **Pod** is a group of containers with shared file systems and namespaces. 
3. This allows containers within the Pod to communicate with each other easily, the containers that belong to the Pod can communicate with one another using `localhost`.

Referenced from [Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/pods/)

## Nodes
1. *Pods* are ran on **Nodes**.
2. A Node may be a virtual or physical machine.
3. Each Node is managed via the Control Plane.
4. Node(s) contain the services that are needed in order to run their respective *Pods*.
5. Typically, you will have several Nodes in a Cluster.
6. Node Components:
    1. *Kubelet*
        1. The agent that runs on each Node in the Cluster. It makes sure that containers are running in their respective pods.
    2. *Container runtime*
        1. Docker (most of the time).
    3. *Kube-proxy*
        1. A network proxy that runs on each Node in the Cluster. This reflects services that are defined within the k8s api on each Node, and can do simple TCP stream or round robin forwarding.
7. Node Management:
    1. The Kubelet on a Node self-registers itself to the Control Plane
    2. A human user manually adds a Node object.

## Services 
1. An abstract way to expose an application running on a set of Pods as a network service.
2. The **bridge** between communicating *Pods* - Given the scenario:
    1. Two nodes, one Pod in each.
    2. pod-pyhon(1.1.1.5) can talk to pod-ngixnx(1.1.1.2) even though they are in different pods.
    3. What happens if pod-nginx dies? Well a new one would be spun up, but it would have a different internal ip than the previous pod... this means pod-python will have a fit because it can't connect to the new pod-nginx
    4. This is where a clusterIP service-nginx(1.1.10.1) would come in. Rather than pod-python connecting to pod-nginx, it would connect to service-nginx which would forward it to any available pod-python. 
  
Referenced from [Kubernetes services simply visually explained by Kim Wuestkamp](https://medium.com/swlh/kubernetes-services-simply-visually-explained-2d84e58d70e5)
