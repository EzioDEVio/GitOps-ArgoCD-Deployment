

### to login to ArgoCD CLI
```
argocd login localhost:8080 --username admin --password 0IFJkYGsG5Gl3Pb5 --insecure
'admin:login'
```

###List cluster
```
argocd cluster list

```
### get pods 
```
kubectl get pods -n argocd
```

###To create an app using ArgoCD CLI 
```
argocd app create my-app `
--repo https://github.com/EzioDEVio/GitOps-ArgoCD-Deployment.git `
--path deployments `
--dest-server https://kubernetes.default.svc `
--dest-namespace default `
--sync-policy auto
```

### To list all apps
```
argocd app list
```

###To list details about your app, in my case named "my-app"
```
argocd app get my-app

```

### To list events
```
kubectl get events -n default
```

### To find out the minikube IP so that can use it along with port number to access your app
```
minikube ip
```

### Minikube status
```
minikube status
```

### To list the ports and cluster-ip 
```
kubectl get svc myapp-service -n default
```

### To bing an app
```
minikube ssh "ping 192.168.49.2"   ###minikube ip:192.168.49.2
```


### To list pods within the namespace for example default or argocd
```
kubectl get pods -n default
```

### To describe pod within cluster. You will need the pod id
```
kubectl describe pod myapp-74cff7b958-qkvtz -n default
```

### To list deployments
```
kubectl get deployments -n default
```

### To delete deployment with arg (deployment name)
```
kubectl delete deployment azurefunction-deployment -n default
```

### To list all the services within namespace
```
kubectl get svc -n default
```

### To delete services
```
kubectl delete svc azurefunction-deployment -n default

```

### To rollout the deployment after new changes such as new image
```
kubectl rollout restart deployment/myapp -n default
```


#########use the following to access your app#############
```
minikube service myapp-service --url -n default
```

http://127.0.0.1:55815
