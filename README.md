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
8. Master vs. Worker Nodes:
    1. Master:
        1. A node which specifically controls a set of worker nodes.
        2. Resembles a Cluster in K8s
        3. A Master Node is composed of:
            1. Kube-API-Server : Acts as the 'front end' to the cluster. All external comms to the cluster is via the api server. 
            2. Kube-Controller-Manager : Runs a set of controllers for running the cluster. Implements governaance across the                   cluster.
            3. ETCD - Cluster state database
            4. Kube-Scheduler : Schedules activities to the worker nodes based on events occurring on the etcd. It holds the nodes                resources plan to determine action of a triggered events. Example, the scheduler would figure out which worker node                would host a newly scheduled POD.
        4. There can be multiple nodes within one cluster (within different AZs).
    2. Worker:
        1. Get's assigned pod(s) to host by the master node
        2. Managed by the master node, a small piece that makes up the distributed workload

## Services 
1. An abstract way to expose an application running on a set of Pods as a network service.
2. The **bridge** between communicating *Pods* - Given the scenario:
    1. Two nodes, one Pod in each.
    2. pod-pyhon(1.1.1.5) can talk to pod-ngixnx(1.1.1.2) even though they are in different pods.
    3. What happens if pod-nginx dies? Well a new one would be spun up, but it would have a different internal ip than the previous pod... this means pod-python will have a fit because it can't connect to the new pod-nginx
    4. This is where a clusterIP service-nginx(1.1.10.1) would come in. Rather than pod-python connecting to pod-nginx, it would connect to service-nginx which would forward it to any available pod-python. 
  
Referenced from [Kubernetes services simply visually explained by Kim Wuestkamp](https://medium.com/swlh/kubernetes-services-simply-visually-explained-2d84e58d70e5)


## Volumes / Storage
1. What is a volume?
    1. Volume is usually a directory with some data - Example: /var/lib
    2. Volumes are accessible in containers in a pod
2. Why? Kubernetes does not give you data persistance right out of the box.
    1. Meaning that on a stateful pod restart, that state would be lost. 
3. What do we need?
    1. We need storage that does not depend on the pod lifecycle
    2. Said storage also needs to be accessible to ALL nodes
        1. Why? You might not know which node a pod might restart to
    3. Storage needs to survive even if cluster crashes
4. Volume types:
    1. Persistent Volume aka *PV* (K8s component)
        1. Think of it as a *cluster resource*
        2. created via YAML file
        3. Needs actual physical storage (local disk on cluster, external nfs server, cloud storage)
        4. PV are NOT namespaced -> accessible to the whole cluster
    2. Persistent Volume Claim aka *PVC*  
        1. Applications have to claim the PV
        2. PVC *claims* the volume with storage size and access type, and the PVC will find the PV that matches the criteria that is being requested
        3. PVC is used within the Pod configuration
    3. ConfigMap and/or Secret components   
        1. Local volume
        2. Not created by PV or PVC
        3. Managed by k8s
        4. Create CM or Secret component, then you can mount them into your pod via the pod yaml file
5. Storage Class aka *SC* 
    1. Storage Class provisions Persistent Volumes dynamically, when PVC claimes it
    2. Storage backend is defined in the sc component, via `provisioner` attribute
    3. Each storage backend has it's own provisioner : internal provisoner == 'kubernetes.io'
    4. Workflow with SC:
        1. Pod claims storage via PVC
        2. PVC requests storage from SC
        3. SC creates PV that meets the requirements of the claim
6. Pod(s) can utilize multiple volume types at once
