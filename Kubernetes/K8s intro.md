<<<<<<< HEAD


=======
>>>>>>> 6eeadd1f6c017cb6ca64b232d3e3e50cbe5c95f3
Container orchestrators are tools which group systems together to form clusters where containers' deployment and management is automated at scale while meeting the requirements
	Fault-tolerance
    On-demand scalability
    Optimal resource usage
    Auto-discovery to automatically discover and communicate with each other
    Accessibility from the outside world
    Seamless updates/rollbacks without any downtime.

_"Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications"._

## Control Panel
__control plane__ node provides a running environment for the control plane agents responsible for managing the state of a Kubernetes cluster,
To *persist the Kubernetes cluster's state*, all cluster configuration data is saved to a **distributed key-value store** which only holds cluster state related data
with external key-value store topology, where the dedicated key-value store hosts have to be separately replicated for HA

__Api Server__ =>  The API Server intercepts RESTful calls, validates and proc
administrative tasks are coordinated by the #kube-apiserver, a central control plane component running on the control plane node

__Scheduler__ => kube-scheduler is to assign new workload objects, such as pods encapsulating containers, to nodes - typically worker nodes
scheduler obtains from the key-value store, via the API Server, resource usage data for each worker node in the cluster. The scheduler also receives from the API Server the new workload object's requirements
	The scheduler is highly configurable and customizable through scheduling policies, plugins, and profiles
	
__controller managers__ are components of the control plane node running controllers or operator processes to regulate the state of the Kubernetes cluster

__Key-value data store__ =>[etcd](https://etcd.io) is an open source project under the [Cloud Native Computing Foundation](https://www.cncf.io) (CNCF). etcd is a strongly consistent, distributed key-value data store used to persist a Kubernetes cluster's state
	- only the API Server is able to communicate with the etcd data store

__Worker Node__
	- Provides running environment for client applications
	- Applications are encapsulated in Pods [smallest unit of work scheduling], that are scheduled on worker nodes [which provide required compute, memory and storage.]
	- Multi-worker k8s cluster network traffic is not routed through the control plane node

__container runtime__
	 k8s lacks capability to *directly* handle containers, supported run times _CRI-Ocontainerd_, _Docker Engine_,  _Mirantis Container Runtime_.

__node agent__ [kubelet]
	agent running on each node control plane and workers communicating with the control panel
	monitors the health of the resources on the pods
	connects container runtimes through a plugin based interface [Container Runtime Interface]

[CRI shims] => {standardized approach to container runtime integration}
	Any continer runtime that implements the CRI could be used by Kubernetes to manage containers.

**Proxy {kube-Proxy}**
	[*network agent*] which runs on each node, control plane and workers, responsible for dynamic updates and maintenance of all networking rules on the nod

### Networking
container runtime _creates an isolated network space_ for each container it starts
	- networking namespace can be shered with other containers or the host operating system
	- Containers grouped into a Pod a [pause container] is started to create a network namespace for the Pod
#pod-to-pod => across nodes




## Building blocks

__ReplicationControllers__ 
	= ensures a specified number of replicas of a Pod are running at any given time the desired version of the application container
	= constantly compares the actual state with the desired state
__Labels__:
	= Allow for grouping/filtering through deployed resources
		selectors => ``
__ReplicaSet__
	= implements the replication and self-healing aspects 
	= can be managed using Deployments

__Deployments__:
	= declarative updates to Pods and ReplicaSets
	= DeploymentController is part of the control plane node's controller manager ensuring the desired state is matched
	**rolling update** is triggered when we update specific properties of the Pod Template

DaemonSets [further reading]
	= Designed to manage node agents
	=  present a distinct feature that enforces a single Pod replica to be placed per Node, on all the Nodes or on a select subset of Nodes.

Application Quotas can be enforced using admission control

__Service__ 
= logically groups Pods and defines a policy to access them
<<<<<<< HEAD
-> grouping is achieved via Labels and Selectors


### Ingress

routing rules are associated with a given Service
ingress controller is watching its corresponding ingress resource, the ingress resource definition manifest needs to include an ingress class name, such as spec.ingressClassName: nginx 

#### Annotations
are not used to identify and select objects. 
Annotations can be used to:
- Store build/release IDs, PR numbers, git branch, etc.
- Phone/pager numbers of people responsible, or directory entries specifying where such information can be found.
- Pointers to logging, monitoring, analytics, audit repositories, debugging tools, etc.
- Ingress controller information.
- Deployment state and revision information.

[ResourceQuota]
[Security Contexts and Pod Security Admission]


## Kubectl

use a kubectl version that is within one minor version difference of your cluster


## Procedure

generate client and server certificates for each Kubernetes component and a client certificate for the Kubernetes admin user.
copy the various certificates to every machine at a path where each Kubernetes component will search for its certificate pair
l generate [Kubernetes client configuration files](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), typically called kubeconfigs, which configure Kubernetes clients to connect and authenticate to Kubernetes API Servers.

Generating kubeconfig files for Kubelets the client certificate matching the Kubelet's node name must be used. This will ensure Kubelets are properly authorized by the Kubernetes Node Authorizer.

The **`kube-scheduler` configuration file** is used to customize how the Kubernetes scheduler behaves. You can control things like leader election, profiles, extenders, plugins, and more.

 etcd member must have a unique name within an etcd cluster. Set the etcd name to match the hostname of the current compute instance:










### kube-apiserver.service systemd

Manages the kube-api, central control plane component
	-> cluster management
	-> Authentication and Authorization
	-> Kubernetes Lifecycle Management



















In a **Kubernetes control plane or node setup**, files are commonly stored under both `/etc` and `/var`, but they serve **different purposes** based on **Linux filesystem standards**:

---

## 📁 `/etc` – Configuration Files (Static Content)

* Used for **static**, system-specific **configuration** files.
* Human-editable and version-controlled configuration.

### 🔹 Common Kubernetes files under `/etc`

| Path                                     | Purpose                                                  |
| ---------------------------------------- | -------------------------------------------------------- |
| `/etc/kubernetes/`                       | Main config dir for kubeadm and control plane components |
| `/etc/kubernetes/manifests/`             | Static Pod YAMLs (used by `kubelet`)                     |
| `/etc/kubernetes/pki/`                   | Certificates and keys (TLS, etcd, front proxy)           |
| `/etc/kubernetes/admin.conf`             | Admin kubeconfig file (for `kubectl`)                    |
| `/etc/cni/net.d/`                        | CNI network plugin configuration                         |
| `/etc/systemd/system/kubelet.service.d/` | Systemd drop-ins for `kubelet` configuration             |

> These files **configure** how the system behaves.

---

## 📁 `/var` – Variable Data (Logs, Runtime, State)

* Used for **runtime data** that **changes frequently**, like logs, PID files, or dynamic content.

### 🔹 Common Kubernetes files under `/var`

| Path                                                                | Purpose                                                            |
| ------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `/var/lib/kubelet/`                                                 | State directory for the kubelet (e.g., volumes, pods, plugin data) |
| `/var/lib/etcd/`                                                    | etcd's persistent database files                                   |
| `/var/lib/kubeadm/`                                                 | Internal kubeadm state                                             |
| `/var/log/pods/`                                                    | Pod logs (on some setups with logging agents)                      |
| `/var/run/kubernetes/` (deprecated, replaced by `/run/kubernetes/`) | Some legacy temporary files                                        |
| `/var/lib/cni/`                                                     | CNI plugin runtime data (e.g., IPAM info, network cache)           |

> These files reflect **state and runtime data** and are usually not edited manually.

---

## 🧠 Summary

| Folder | Used For             | Contents                           | Mutable?        |
| ------ | -------------------- | ---------------------------------- | --------------- |
| `/etc` | Configuration        | Certs, manifests, kubeconfig       | ❌ No (static)   |
| `/var` | Runtime state & data | kubelet state, etcd data, CNI data | ✅ Yes (dynamic) |

---

Let me know if you want a diagram or example of how kubeadm lays out these files.
=======
-> grouping is achieved via Labels and Selectors
>>>>>>> 6eeadd1f6c017cb6ca64b232d3e3e50cbe5c95f3
