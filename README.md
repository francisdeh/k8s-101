# k8s ⚡ 101

```bash
minikube start
```

![Screenshot 2023-08-02 at 9.39.13 AM.png](data/Screenshot_2023-08-02_at_9.39.13_AM.png)

```bash
kubectl run hello-world --image=amigoscode/kubernetes:hello-world --port=80
kubectl port-forward --port=8080
```

![Screenshot 2023-08-02 at 9.40.40 AM.png](data/Screenshot_2023-08-02_at_9.40.40_AM.png)

```bash
kubectl delete pod hello-world
kubectl get pods
kubectl get pods -A
```

![Screenshot 2023-08-02 at 10.08.56 AM.png](data/Screenshot_2023-08-02_at_10.08.56_AM.png)

```bash
minikube ssh
minikube stop
minkube delete

minikube start --nodes=2
```

![Screenshot 2023-08-02 at 10.04.33 AM.png](data/Screenshot_2023-08-02_at_10.04.33_AM.png)

```bash
minikube get nodes
minikube ip
minikube ip --node=minikube-m01
minikube stats
minikube logs
minikube logs -f
minkube logs --node=minikube-m01 -f
```

## Pod

Smallest deployable unit - part of a node. ie worker node. The control plane (master node) has its on unique pods necessary for running k8 functionalities.

![Screenshot 2023-08-02 at 10.19.52 AM.png](data/Screenshot_2023-08-02_at_10.19.52_AM.png)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
  labels:
    name: hello-world
spec:
  containers:
  - name: hello-world
    image: amigoscode/kubernetes:hello-world
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
    ports:
      - containerPort: 80
```

```bash
kubectl apply -f pod.yaml # create pod declaratively
cat pod.yaml | kubectl apply -f - # apply from piped content
kubectl apply -f . # apply all k8 resource files
kubectl get pods -w # watch the running status of pods as they update

kubectl delete -f pod.yaml 
```

![Screenshot 2023-08-02 at 2.39.46 PM.png](data/Screenshot_2023-08-02_at_2.39.46_PM.png)

![Screenshot 2023-08-02 at 2.43.09 PM.png](data/Screenshot_2023-08-02_at_2.43.09_PM.png)

```bash
kubectl get all # get all resources. shows the clusters
kubectl get all -A # get all resources including pods, services with clusters etc

kubectl get pods -n kube-system # pods for only kube-system namespace. Excludes default
kubectl get all -n kube-system
kubectl get namespaces # or
kubectl get ns

kubectl describe pod hello-world # describe pod
kubectl get pod hello-world -o wide # describes pod in one line. --output
# wide, json, yaml
```

**Logs**

```bash
kubectl logs hello-world
kubectl logs hello-world -f

# to target the logs for a specific container inside the pod,
kubectl logs hello-world -c hello-world
```

![Screenshot 2023-08-02 at 3.14.58 PM.png](data/Screenshot_2023-08-02_at_3.14.58_PM.png)

**A pod with two containers**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
  labels:
    name: hello-world
spec:
  containers:
  - name: hello-world
    image: amigoscode/kubernetes:hello-world
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
    ports:
      - containerPort: 80
	- name: ola-mundo
    image: amigoscode/kubernetes:hello-world
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
    ports:
      - containerPort: 82
```

**Executing** **commands**

```yaml
kubectl exec -it hello-world -- sh
#_

kubectl exec -it hello-world -c hello-world -- /bin/sh
kubectl exec hello-world -- ls

kubectl api-resources
```

cheat sheet [here](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

### Deployments

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: amigoscode/kubernetes:hello-world
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80
```

```bash
kubectl apply -f deployments/deployment.yaml
kubectl get po
kubectl get deployments
kubectl describe deployments hello-world
kubectl delete deployment hello-world
```

### ReplicaSet w/ Deployment

By default, a deployment comes with one replicaset

```bash
kubectl get replicaset # or rs
kubectl port-forward deployment/hello-world 8081:80
```

![Screenshot 2023-08-04 at 11.29.26 AM.png](data/Screenshot_2023-08-04_at_11.29.26_AM.png)

```bash
...
# add to deployment file
spec:
  replicas: 3
...

kubectl apply -f deployments/deployment.yaml
kubectl get pods
# or use this to get all stats together
kubectl get all
```

![Screenshot 2023-08-04 at 11.35.25 AM.png](data/Screenshot_2023-08-04_at_11.35.25_AM.png)

![Screenshot 2023-08-04 at 11.38.46 AM.png](data/Screenshot_2023-08-04_at_11.38.46_AM.png)

```bash
kubectl describe rs hello-world

kubectl rollout history deployment hello-world
kubectl rollout undo deployment hello-world
```

![Screenshot 2023-08-04 at 2.29.13 PM.png](data/Screenshot_2023-08-04_at_2.29.13_PM.png)

**Services**

![Screenshot 2023-08-04 at 2.49.15 PM.png](data/Screenshot_2023-08-04_at_2.49.15_PM.png)

![Screenshot 2023-08-04 at 2.53.37 PM.png](data/Screenshot_2023-08-04_at_2.53.37_PM.png)