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