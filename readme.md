# ✅ Project Setup 

# Part One 
We’ll build a small project to test communication between pods using Weave Net as the CNI in a Kubernetes cluster powered by Minikube with Docker on Windows.

and then we will apply different network policies to test the hhow the connection will be.

## 🖥️ 1. Prerequisites
Ensure you have the following installed on your Windows machine:

Docker Desktop

Minikube

kubectl

 
## 🚀 2. Start Minikube with Docker Driver and Pod CIDR
Use this command to start Minikube with Docker and Weave-compatible network settings:

```

minikube start --driver=docker --cni=false --extra-config=kubelet.pod-cidr=10.32.0.0/12
```
📌 --cni=false means we’ll manually install Weave Net (instead of letting Minikube auto-pick one).

## 🌐 3. Install Weave Net CNI
Now install Weave Net:

```
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```
Wait a few moments and ensure all pods in the kube-system namespace are running:

```
kubectl get pods -n kube-system
```


## 🧪 4. Deploy a Simple Test App
Create two pods: a backend that responds to requests and a frontend that curls the backend.

**Note** 

the pods yaml files are shown in the repo just apply them 
```
kubectl apply -f frontend.yaml

kubectl apply -f backend.yaml
```

Exec into pod with container name specified explicitly
Try:
```
kubectl exec -it frontend -c curl -- curl backend:80
```
Notice the **-c curl** flag specifies the container.

# Part Two

# 🛡️ Kubernetes Network Policy Example: Allow Frontend → Backend Only

## 1-Create a Network Policy YAML to restrict backend ingress
 ### its already created in the repo by the name (allow-frontend-to-backend)

 apply the network policy 

 ```
 kubectl apply -f allow-frontend-to-backend.yaml
```
now try to get into the frontend pod and ping the backend :

```
kubectl exec -it frontend -- sh
```
```
ping <backend-ip>

you can verify the list of ip's by:
kubectl get pods -o wide
```

as you can see backend can indeed see front end

now lets try with the nginx pod

```
kubectl exec -it nginx -- /bin/bash
root@nginx:/# apt-get update
root@nginx:/# apt-get install iputils -ping -y
root@nginx:/# ping <frontend-ip>
```

as you can see this won't work

because its created with different label 

**app: nginx-app**

## 2-Create a default deny policy for backend ingress
**this will block all incoming traffic to backend except those allowed explicitly,**

**just apply the yaml file name (deny-all-ingress-backend)**

this will deny all the traffic that is coming towards the backend

## 🎮 Now It’s Your Turn!

That’s the full project setup! Now go ahead and play around with it:

- Tweak the **deny-all** policy.
- Modify or extend the **allow rules**.
- Break things on purpose — and fix them.
- Try adding **egress rules**, **namespaces**, or **labels**.

This is your sandbox. The best way to learn networking and policies in Kubernetes is 