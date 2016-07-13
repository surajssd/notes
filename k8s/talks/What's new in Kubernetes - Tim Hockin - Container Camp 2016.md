# What's new in Kubernetes - Tim Hockin - Container Camp 2016


### Desc:

Kubernetes is a very fast-paced project. Unless you are involved in it day-to-day (and even if you are!) it can be challenging to keep track of all the new features. This talk will focus on a series of features that come with the v1.2 release of Kubernetes, and some clues as to what is coming in v1.3.

**Note**: Features in 1.2 release


### Multi-Zone Clusters (Ubernetes lite)

- **Goal**: zone-fault tolerance for applications
- Run applications in multiple clusters and k8s will take care of bringing pods up on those zones.
- No API change.

### Deployments

- **Goal**: updates-as-a-service
- Rolling updates
- Deployment objects creates a replication object for you, when deployment is updated new Replication controller for you.
- `kubectl edit` or `kubectl apply`
- Rolling updates happen server side, previously they happened client side.
- Undo - to rollback to previous version


### DaemonSets

- **Problem**: how to run a Pod on every node/subset of nodes?
- Similar to ReplicationController
- Makes sure that one pod is running on all the nodes

### HorizontalPodAutoscalers

- **Goal**: Automatically scale pods as needed
- Based on CPU utilization
- Hooks into the replication controller

### Jobs

- Run to completion, as opposed to run-forever
- Express parallelism vs. required completions

### Secrets

- **Goal**: grant a pod access to a secured something
- don't put secrets in the container image!
- Manage secrets via the K8s API
- Inject secrets as virtual volumes into your pods
	- Late-bindings, tmpfs - never touches disk
	- also available as environment variables of your choice

### ConfigMaps

- **Goal**: manage app configuration
- If you have some config you wanna inject into your application, you don't wanna build your config into your app image because, config is more likely to change.
- Injected similar to secrets

### HTTP Load-Balancing / Ingress (L7 LB)

- Many apps are HTTP/HTTPS
- HAProxy, NGNIX, AWS and GCE 
- Ingress maps incoming traffic to backend services
	- by HTTP host headers
	- by HTTP URL paths

### PersistentVolumes

- A higher level storage abstraction
- Admin provisions them, users claims them

### Network Isolation

- Restrict pod to pod traffic or across Namespaces


### Links:

- **Video**: https://www.youtube.com/watch?v=k6vCEc86ihI
- **Slides**: https://speakerdeck.com/thockin/kubernetes-one-year-later
- **Event**: Container Camp SF 2016