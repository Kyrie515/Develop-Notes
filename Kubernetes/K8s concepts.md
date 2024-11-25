## Pods

Pod is the smallest instance of app and smallest object can be created in K8s. When the load is increasing, we can:

1. Create a new pod in the same node.
2. Deploy a new node with a new instance.



How can we deploy a pod:

`kubectl run nginx --image=nginx` will create a pod with a single container instance of nginx.

`kubectl get pods` we can see the info of pods.



### Pods with YAML

Kubenetes uses yaml as the input of configuration of objects like pods, replica...etc.

For pod definition, the yaml always looks like:

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: myapp-pod
	labels:
		app: myapp
		type: front-end

#provide addtional info for our app
spec: 
	containers:
	- name: nginx-container
		image: nginx
```

let us named the file `pod-def.yaml`, then we can create the pod by:

`kubectl create -f pod-def.yaml`



## Replication Controller and ReplicaSet

Replication controller is providing the function that can run multiple instances of a single pod at the same time to prevent some pods are out of service. It is called **High Availability**.

1. Load Balancing
2. Scaling

Replication Controller is replaced by Replica Set recently. Replica Set is recommended way.



### Replication Controller with YAML

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
	name: myapp-rc
	labels:
		app: myapp
		type: front-end
		
#this is most important part
#because the replication controller is to create multiple instances of a pod, we should define a pod here
spec:
	template: #template of definition of a pod
		metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    #provide addtional info for our app
    spec: 
      containers:
      - name: nginx-container
        image: nginx
        
  replicas: 3 # the number of pods that needed to be replicated
		
```



### Replica Set with YAML

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: myapp-rc
	labels:
		app: myapp
		type: front-end
		
#this is most important part
#because the ReplicaSet is to create multiple instances of a pod, we should define a pod here
spec:
	template: #template of definition of a pod
		metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    #provide addtional info for our app
    spec: 
      containers:
      - name: nginx-container
        image: nginx
        
  replicas: 3 # the number of pods that needed to be replicated
  selector: 
  	matchLabels:
  		type: front-end
		
```

what is `selector`? To help the ReplicaSet know which pods to be monitored.

If we need to scale the replica, we can:

1. update the yaml file and use `kubectl replace -f replicaset-def.yaml`
2. `kubectl scale --replicas=6 -f replicaset-def.yaml`