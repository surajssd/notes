# Container clusters with Kubernetes - Tim Hockin

### Intro

- Early containers were mainly `chroot`, `cgroups`, etc.
- Everything in google runs in containers.

### Kubernetes high level overview

- User provides details of the application to run and *apiserver* is responsible for reading it and processing it further.
- *Scheduler* on master then decides on which *node* to run this application as pod on? To decide this k8s has things like: *constraints*, *affinity*, *resource mapping*, etc.
- After the pods are scheduled, the nodes will do docker pull and pull container from the docker registry.

### Container Cluster

1. **Setting up a cluster**
	- Choose a cloud
	- Choose a node OS
	- provision machines
	- Configure networking
	- start cluster services
	- manage nodes
2. **Using a cluster**
	- Run pods and containers
	- Don't make developers deal with cluster administration.

### Networking

- Pod IPs are **routable**, docker default is private IP
- Pods can reach each other without NAT, even across nodes
- **No brokering** of port numbers

### Pods

- Multiple containers, scheduling and placement unit.
- Tightly coupled
- Shared namespace, share IP Address and localhost, share IPC
- e.g. data puller and web server.

### Services

- A group of pods that work together, grouped by selector.
- Defines access policy, "load balanced" or "headless"
- Gets a stable virtual IP and port, called the service portal, also a DNS name.
- Virtual IP is captured by kube-proxy, watches the service constituency, updates when backend change

### kube-proxy

- Runs on every machine
- Watches services and endpoints
- Writes IP table rules to direct traffic

### Volumes

- Very similar to docker's concept
- Pod scoped storage
- share the pod's lifetime
- Many different volume plugins are supported right now.

### Persistent Volumes

- A higher level abstraction - insulation from any one cloud environment
- Admin provisions them, user claim them using, Persistent Volume Claim
- Can be handed-off between pods and lives until user is done with it
- Dynamically *scheduled*  and managed, like nodes and pods.

### Ref:

- Video: https://www.youtube.com/watch?v=BaHp8YXIsD0
- Held at: Container Camp, a community conference about software containers. April 17, 2015, San Francisco.