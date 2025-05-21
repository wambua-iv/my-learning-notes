
Smallest kubernetes workload object [logical collection of one or more containers that share same namespaces and cgroups, access to mount same external storage]

Smallest kubernetes workload object [logical collection of one or more containers that share same namespace, access to mount same external storage]
	- represents an instance of an application
	- cannot self-heal
```yaml
apiVersion: v1  
kind: Pod 
metadata:
  name: nginx-pod
  labels:
    run: nginx-pod
spec:
  containers:
	- name: nginx-pod  
    image: nginx:1.22.1  
    ports:
    - containerPort: 80

```

-**apiVersion** field must specify "v1" for the Pod object definition. -
	-second required field is **kind** specifying the Pod object type. 
	-third required field **metadata**, holds the object's name and optional labels and annotations. The fourth required field spec marks the beginning of the block defining the desired state of the Pod object - also named the PodSpec.


#### Pod life-cycle states
=> Pending
	- Pod has been accepted into the cluster
	- Awaiting scheduling and container downloading
=> Running 
	- container has been bound to a node
	- containers are running or starting
=> Succeeded
	- all containers are in success state
=> Failed 
	- All containers in the pod have terminated
=> Unknown
	- error in communicating with the node and pod status could not be retrieved

### Services
=> abstraction that defines a logical set of **Pods** and a policy by which to access them
=> Provide a stable endpoint (IP and DNS name) to access pods that are ephemeral.
	- **ClusterIP** (default):
	    - Accessible only within the cluster.
	    - Used for internal communication between services (e.g., frontend calling backend).
	    - can be used as a single endpoint for a set of pods
	- **NodePort**:
	    - Exposes the Service on a static port on each Node’s IP.
	    - Can be accessed externally via `NodeIP:NodePort`.
	    - extends the ClusterIP by adding simple routing rules
	- **LoadBalancer**:
	    - Provisions an external load balancer (if supported by the cloud provider).
	    - Exposes the service publicly with a single IP.
	- **ExternalName**:
	    - Maps a service to a DNS name (e.g., external databases).
	    - No real selector; uses DNS CNAME records.    
	- **Headless Service** (`ClusterIP: None`):
	    - Used when you want direct Pod access, e.g., for stateful applications or DNS-based service discovery.

### Pod controller Objects

##### =>ReplicaSet 
- ensures a desired number of pods is running at any given time
##### => Deployment
- can be used to describe the complete application lifecycle
- managing multiple ReplicaSets that get updated
- perfect to run stateless applications in Kubernetes.
##### => StatefulSet
- used to run stateful applications like databases on Kubernetes
- StatefulSets try to retain IP addresses of pods and give them a stable name,
##### => DaemonSet 
- Ensures that a copy of a Pod runs on all (or some) nodes of your cluster
##### => Job
- Creates one or more Pods that execute a task and terminate afterwards
##### => CronJob
- CronJobs add a time-based configuration to jobs. This allows running Jobs periodically
- CronJob creates a Job object approximately once per execution time of its schedule
- CronJob is only responsible for creating Jobs that match its schedule, and the Job in turn is responsible for the management of the Pods it represents



### Volumes
Storage abstractions that allow various storage technologies to be used essentially a mount point on the container's file system backed by a storage medium
	- [PersistentVolumes] -> abstract description for a slice of storage. The object configuration holds information like type of volume, volume size, access mode and unique identifiers and information how to mount it.
	- [PersistentVolumeClaims] -> request for storage by a user. If the cluster has multiple persistent volumes, the user can create a PVC which will reserve a persistent volume according to the user's needs. 

### Config Maps
decouple the configuration details from the container image.
pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers

using-  nm xcvb a ConfigMap:
- Mount a ConfigMap as a volume in Pod
- Map variables from a ConfigMap to environment variables of a Pod.

Create a ConfigMap from a File => first create [.properties] file



-third required field **metadata**, holds the object's name and optional labels and annotations. The fourth required field spec marks the beginning of the block defining the desired state of the Pod object - also named the PodSpec.

=> Authentication => Authorization => Admission 

