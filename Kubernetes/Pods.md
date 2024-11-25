## Pods

Pod is the smallest instance of app and smallest object can be created in K8s. When the load is increasing, we can:

1. Create a new pod in the same node.
2. Deploy a new node with a new instance.



How can we deploy a pod:

`kubectl run nginx --image=nginx` will create a pod with a single container instance of nginx.

`kubectl get pods` we can see the info of pods.



## Pods with YAML

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