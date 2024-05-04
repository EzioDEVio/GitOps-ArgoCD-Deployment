[![GitOps with ArgoCD kubernetes Deployment](https://github.com/EzioDEVio/GitOps-ArgoCD-Deployment/actions/workflows/argocd.yml/badge.svg)](https://github.com/EzioDEVio/GitOps-ArgoCD-Deployment/actions/workflows/argocd.yml)  [![KubeLinter Scan](https://github.com/EzioDEVio/GitOps-ArgoCD-Deployment/actions/workflows/KubeLinter.yml/badge.svg)](https://github.com/EzioDEVio/GitOps-ArgoCD-Deployment/actions/workflows/KubeLinter.yml) 

# Deploying a Node.js App on Kubernetes with Argo CD 🚀

This guide walks you through deploying a Node.js application using Minikube and Argo CD, leveraging the GitOps methodology. By the end of this tutorial, you'll have a solid understanding of deploying applications on Kubernetes with automatic syncing through Argo CD.

## Prerequisites 📋

Before you start, make sure you have the following installed on your local machine:

- Docker 🐳
- Minikube 🚤
- kubectl 🛠️
- Argo CD CLI 🖥️
- Git 🌿

## Step 1: Start Minikube

Start your Minikube environment. This is your local Kubernetes cluster, ideal for development and testing.

```bash
minikube start
```

## Step 2: Enable Minikube Addons

Enable necessary Minikube addons like the dashboard and ingress to enhance functionality.

```bash
minikube addons enable dashboard
minikube addons enable ingress
```

## Step 3: Install Argo CD

Install Argo CD on your Minikube cluster using kubectl:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Step 4: Access Argo CD

Use port forwarding to access the Argo CD UI through the Minikube IP:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

**Login Details:**
- **Username**: admin
- **Password**: Retrieve the password using:
  
  ```bash
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode; echo
  ```

## Step 5: Create a GitHub Repository

Create a repository to store your Kubernetes YAML files. This repository will be used by Argo CD to sync your application's state.

1. **Create a new repository** on GitHub.
2. **Clone your repository**:

```bash
git clone https://github.com/EzioDEVio/GitOps-ArgoCD-Deployment.git
cd GitOps-ArgoCD-Deployment
```

## Step 6: Add Kubernetes Manifests

Create `deployment.yaml` and `service.yaml` under a `deployments` directory in your repo, see my other repo for more deep dive and details on how to deploy kubernetes cluster with yaml files manifsts:

**Example:**

**deployment.yaml**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: yourusername/yourapp:latest
        ports:
        - containerPort: 3000
```

**service.yaml**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30001
  selector:
    app: myapp
```

## Step 7: Commit and Push Your Changes

```bash
git add .
git commit -m "Add Kubernetes deployment manifests"
git push
```

## Step 8: Configure Argo CD to Monitor Your Repository

Create an Argo CD application linked to your GitHub repository:

```bash
argocd app create myapp \
  --repo https://github.com/yourusername/yourrepository.git \
  --path deployments \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

## Step 9: Sync Your Application

Manually sync your application from the Argo CD dashboard, or use the CLI:

```bash
argocd app sync myapp
```

## Step 10: Verify Deployment

Check the deployment status in Argo CD or by using kubectl:

```bash
kubectl get all
```

## Accessing the ArgoCD and the app

# List current running pods
```
kubectl get pods -n argocd
```

# Login to argocd an access it in the browser
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
# Open up the localhost on the browser           
```
https://localhost:8080
```
# The default user name is "admin" the password can be retrieved by using the following command:

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode; echo
```
# accessing ArgoCD using the CLI
```
argocd login localhost:8080 --username admin --password <the password from the step above> --insecure
```
# Accessing the app you deployed in ArgoCD
```
minikube service myapp-service --url -n default
```
minikube service myapp-service --url -n default
http://127.0.0.1:58325
❗  Because you are using a Docker driver on windows, the terminal needs to be open to run it.

The following url will be the app you deployed to ArgoCD: http://127.0.0.1:58325

## Troubleshooting

# Application Not Accessible:
Check if the service is up and NodePort is correctly mapped. Ensure your firewall or network settings are not blocking the connection.
Pods Not Starting:
If pods are in a pending or error state:
```
kubectl get pods -n default
kubectl describe pod <pod-name> -n default
```
# Service Unreachable:
Verify Minikube's network settings and ensure the tunnel is running if using minikube service.
Resource Usage Monitoring:
Monitor resource usage to ensure the application is not consuming excessive resources:
```
kubectl top pod <pod-name> -n default
```
## Additional Notes
Always ensure your Kubernetes environment and tools are up to date.
Backup important data and configurations regularly.
Feedback and Contributions
Feedback and contributions to this project are welcome. Please open an issue or pull request in the GitHub repository.

## Conclusion 🎉

You now have a Node.js application running on Kubernetes, managed via Argo CD using GitOps principles. This setup exemplifies a modern CI/CD pipeline that is scalable, manageable, and transparent. 


### Additional Notes

- **Customization**: You may need to replace placeholders (like `your-username/your-repo`) with actual values relevant to your project.
- **Repository Links**: Adjust links to match the actual URLs where your code and Docker images are hosted.
- **ArgoCD Configuration Details**: This README assumes ArgoCD is already configured and operational within the cluster. If this is not the case, you might need to include setup details or refer to official ArgoCD documentation for initial setup instructions.

This README.md provides a comprehensive guide that not only helps users understand how to get the application running but also how to manage deployments through a GitOps workflow using ArgoCD.


