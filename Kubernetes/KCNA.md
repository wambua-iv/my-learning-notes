Cloud native technologies 
	- build and run scalable applications in modern, dynamic environments such as public, private, and hybrid clouds. Containers, service meshes, microservices, immutable infrastructure, and declarative APIs exemplify this approach.
	- Breaking  a technology into smaller pieces making it more manageable
[xteristics]
	=> High levels of Automation _continuous Integration/Continuous Delivery_
	=> Self Healing ->  including health checks
	=> Scalable -> handling more load while maintaining pleasant user experience
	=> (Cost-) Efficient -> optimize infrastructure utilization
	=> Easy to Maintain
Metrics for Autoscaling
 => CPU and Memory utilization
 => Time and business metrics

[Vertical Scaling] => changing the underlying hardware
[horizontal scaling] => spewing new compute/storage resources

Project Management Stages
	Sandbox stage
	Incubating stage
	Graduation stage

Namespaces are used to isolate various resources. The Linux Kernel 5.6 currently provides 8 namespaces:

- **pid** - process ID provides a process with its own set of process IDs.
- **net** - network allows the processes to have their own network stack, including the IP address.
- **mnt** - mount abstracts the filesystem view and manages mount points.
- **ipc** - inter-process communication provides separation of named shared memory segments.
- **user** - provides process with their own set of user IDs and group IDs.
- **uts** - Unix time sharing allows processes to have their own hostname and domain name.
- **cgroup** - a newer namespace that allows a process to have its own set of cgroup root directories.
- **time** - the newest namespace can be used to virtualize the clock of the system.

networking problems that need to be solved:
	* Container-to-Container communications
	    -This can be solved by the Pod concept as we'll learn later.
    * Pod-to-Pod communications
	    -This can be solved with an overlay network.
    * Pod-to-Service communications
	    -It is implemented by the kube-proxy and packet filter on the node.
    * External-to-Service communications
	    -It is implemented by the kube-proxy and packet filter on the node.

[to note on networking]
- All pods can communicate with each other across nodes.
- All nodes can communicate with all pods.
- No Network Address Translation (NAT).

In Kubernetes, every Pod gets its own IP address, so there is no manual configuration involved. Moreover, most Kubernetes setups include a DNS server add-on called core-dns, which can provide service discovery and name resolution inside the cluster.

Object [abstract resources, used to describe how workloads should be handled] descriptor required fields
- **apiVersion**  
    Each object can be versioned. That means the data structure of the object can change between different versions.
- **kind**  
    The kind of object that should be created.
- **metadata**  
    Data that can be used to identify it. A **name** is required for each object and must be unique. You can use namespaces if you need multiple objects with the same name.
- **spec**  
    The specification of the object. Here you can describe your desired state. Be cautious, since the structure for the object can change with its version!


#### Auto Scaling Objects
[Horizontal Pod Autoscaler]
	- watches Deployments or ReplicaSets and increase the number of Replicas if a certain threshold is reached
[Cluster Autoscaler]
	- Adds new worker nodes to the cluster if the demand increases


Kube-scheduler
[Label selectors]
	[nodeSelector]: node node selection constraint. You can added to  the Pod specification to specify the node labels you want the target node to have.
	[Affinity and anti-affinity]: expands the types of constraints you can define and give you more control over the selection logic
	[nodeName]: nodeName is a field in the Pod spec. overrules using nodeSelector or affinity and anti-affinity rules. scheduler ignores the Pod and the kubelet on the named node tries to place the Pod on that node.
	[Pod topology spread constraints]: se used to control how Pods are spread across your cluster among failure-domains
	[Tolerations]: applied applied to pods, allowing the scheduler to schedule pods with matching taints. but they don't guarantee scheduling: the scheduler also evaluates other parameters as part of its function. [Specified on the podSpec]
	[Taints]: allow applied to a node to repel a set of pods
		- Key value pair and an __effect__ {key=value:Effect}
		- effect must be **NoSchedule**, **PreferNoSchedule** or **NoExecute**