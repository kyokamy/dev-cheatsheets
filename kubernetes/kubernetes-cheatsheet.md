# ☸️ The Ultimate Kubernetes Cheatsheet

### *Because `kubectl` commands are like passwords - you'll forget them the moment you need them*

---

## 🎬 Act I: Getting Started (a.k.a. "Welcome to the Cluster")

### Installation & Setup

```bash
# Install kubectl (Linux)
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install kubectl (macOS)
brew install kubectl

# Install kubectl (Windows - via Chocolatey)
choco install kubernetes-cli

# Install minikube (local Kubernetes)
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start minikube
minikube start
minikube start --driver=docker    # Use Docker driver
minikube start --driver=virtualbox # Use VirtualBox

# Check cluster connection
kubectl cluster-info
kubectl get nodes
```

### Context & Configuration

```bash
# View current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context-name>

# View config file location
kubectl config view
kubectl config view --raw          # Show full config

# Set namespace for current context
kubectl config set-context --current --namespace=<namespace>
```

---

## 📦 Act II: Pods (The Basic Unit of Life)

### Pod Operations

```bash
# List all pods
kubectl get pods
kubectl get pods -A                # All namespaces
kubectl get pods -n <namespace>    # Specific namespace
kubectl get pods -o wide           # More details

# Get pod details
kubectl describe pod <pod-name>
kubectl describe pod <pod-name> -n <namespace>

# Get pod logs
kubectl logs <pod-name>
kubectl logs <pod-name> -f         # Follow logs
kubectl logs <pod-name> --tail=100 # Last 100 lines
kubectl logs <pod-name> -c <container> # Multi-container pod

# Execute command in pod
kubectl exec <pod-name> -- <command>
kubectl exec <pod-name> -it -- /bin/bash  # Interactive shell
kubectl exec <pod-name> -c <container> -- <command>

# Create pod from YAML
kubectl apply -f pod.yaml
kubectl create -f pod.yaml

# Delete pod
kubectl delete pod <pod-name>
kubectl delete pod <pod-name> -n <namespace>
kubectl delete -f pod.yaml

# Port forward to pod
kubectl port-forward <pod-name> 8080:80
kubectl port-forward <pod-name> 8080:80 -n <namespace>
```

### Pod Creation Examples

```yaml
# Basic pod
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

```bash
# Create pod from image
kubectl run nginx-pod --image=nginx:latest --port=80

# Create pod with environment variables
kubectl run my-pod --image=my-image --env="KEY=value"

# Create pod with resource limits
kubectl run my-pod --image=my-image --limits="memory=512Mi,cpu=500m"
```

---

## 🚀 Act III: Deployments (The Workhorse)

### Deployment Operations

```bash
# Create deployment
kubectl create deployment <name> --image=<image>
kubectl create deployment nginx --image=nginx:latest
kubectl create deployment nginx --image=nginx:latest --replicas=3

# Get deployments
kubectl get deployments
kubectl get deployments -n <namespace>
kubectl get deploy                  # Short form

# Describe deployment
kubectl describe deployment <deployment-name>

# Scale deployment
kubectl scale deployment <name> --replicas=5
kubectl scale deployment nginx --replicas=3

# Update deployment image
kubectl set image deployment/<name> <container>=<new-image>
kubectl set image deployment/nginx nginx=nginx:1.21

# Rollout status
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>

# Rollback deployment
kubectl rollout undo deployment/<name>
kubectl rollout undo deployment/<name> --to-revision=2

# Pause/Resume rollout
kubectl rollout pause deployment/<name>
kubectl rollout resume deployment/<name>

# Delete deployment
kubectl delete deployment <name>
```

### Deployment YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

---

## 🌐 Act IV: Services (Networking Made Simple)

### Service Operations

```bash
# List services
kubectl get services
kubectl get svc                     # Short form
kubectl get svc -A                 # All namespaces

# Describe service
kubectl describe service <service-name>

# Expose deployment as service
kubectl expose deployment <name> --type=LoadBalancer --port=80
kubectl expose deployment nginx --type=NodePort --port=80

# Create service from YAML
kubectl apply -f service.yaml

# Delete service
kubectl delete service <service-name>
```

### Service Types

```yaml
# ClusterIP (default, internal only)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080

---
# NodePort (expose on node IP)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080

---
# LoadBalancer (cloud provider LB)
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
```

---

## 📋 Act V: ConfigMaps & Secrets (Configuration Management)

### ConfigMap Operations

```bash
# Create ConfigMap from literal
kubectl create configmap <name> --from-literal=key=value
kubectl create configmap app-config --from-literal=database_url=postgres://...

# Create ConfigMap from file
kubectl create configmap <name> --from-file=<file>
kubectl create configmap app-config --from-file=config.properties

# Create ConfigMap from directory
kubectl create configmap <name> --from-file=<directory>

# List ConfigMaps
kubectl get configmaps
kubectl get cm                     # Short form

# Describe ConfigMap
kubectl describe configmap <name>

# Get ConfigMap as YAML
kubectl get configmap <name> -o yaml

# Delete ConfigMap
kubectl delete configmap <name>
```

### Secret Operations

```bash
# Create secret from literal
kubectl create secret generic <name> --from-literal=key=value
kubectl create secret generic db-secret --from-literal=password=secret123

# Create secret from file
kubectl create secret generic <name> --from-file=<file>
kubectl create secret tls <name> --cert=<cert-file> --key=<key-file>

# List secrets
kubectl get secrets
kubectl get secrets -n <namespace>

# Describe secret
kubectl describe secret <name>

# Decode secret (base64)
kubectl get secret <name> -o jsonpath='{.data.password}' | base64 -d

# Delete secret
kubectl delete secret <name>
```

### Using ConfigMaps and Secrets in Pods

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: my-app:latest
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_url
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
    envFrom:
    - configMapRef:
        name: app-config
    - secretRef:
        name: db-secret
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

---

## 💾 Act VI: Persistent Volumes (Storage)

### Volume Operations

```bash
# List persistent volumes
kubectl get pv                     # PersistentVolumes
kubectl get pvc                    # PersistentVolumeClaims

# Describe volume
kubectl describe pv <pv-name>
kubectl describe pvc <pvc-name>

# Create PVC from YAML
kubectl apply -f pvc.yaml

# Delete PVC
kubectl delete pvc <pvc-name>
```

### Volume Examples

```yaml
# PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard

---
# Pod using PVC
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: app
    image: my-app:latest
    volumeMounts:
    - name: storage
      mountPath: /data
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: my-pvc
```

---

## 🏷️ Act VII: Namespaces (Organization)

### Namespace Operations

```bash
# List namespaces
kubectl get namespaces
kubectl get ns                     # Short form

# Create namespace
kubectl create namespace <name>
kubectl create ns production

# Switch to namespace
kubectl config set-context --current --namespace=<namespace>

# Get resources in namespace
kubectl get all -n <namespace>

# Delete namespace (deletes everything in it!)
kubectl delete namespace <name>
```

### Namespace YAML

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    name: production
    environment: prod
```

---

## 🔍 Act VIII: Debugging & Troubleshooting

### Common Debugging Commands

```bash
# Get events
kubectl get events
kubectl get events --sort-by='.lastTimestamp'
kubectl get events -n <namespace>

# Describe resource (see what's wrong)
kubectl describe pod <pod-name>
kubectl describe node <node-name>
kubectl describe deployment <deployment-name>

# Check pod status
kubectl get pods --field-selector=status.phase!=Running
kubectl get pods --field-selector=status.phase=Pending

# Get pod logs (multiple containers)
kubectl logs <pod-name> -c <container-name>
kubectl logs <pod-name> --all-containers=true

# Previous container logs (crashed containers)
kubectl logs <pod-name> --previous

# Top resources (CPU/Memory)
kubectl top nodes
kubectl top pods
kubectl top pods -n <namespace>

# Get resource usage
kubectl get --raw /metrics

# Check API resources
kubectl api-resources
kubectl api-versions
```

### Common Issues & Fixes

```bash
# Pod stuck in Pending
kubectl describe pod <pod-name>    # Check events for why

# Pod in CrashLoopBackOff
kubectl logs <pod-name> --previous # Check previous logs
kubectl describe pod <pod-name>    # Check events

# Image pull errors
kubectl describe pod <pod-name>    # Check image pull secrets

# Not enough resources
kubectl describe node              # Check node resources
kubectl top nodes                  # Check actual usage

# Service not working
kubectl get endpoints <service-name>  # Check if endpoints exist
kubectl get pods -l app=<label>       # Check if pods match selector
```

---

## 🔧 Act IX: Advanced Operations

### Jobs & CronJobs

```bash
# Create job
kubectl create job <name> --image=<image> -- <command>
kubectl create job my-job --image=busybox -- echo "Hello"

# List jobs
kubectl get jobs

# Describe job
kubectl describe job <name>

# Delete job
kubectl delete job <name>
```

```yaml
# CronJob example
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-job
spec:
  schedule: "0 2 * * *"  # 2 AM daily
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: backup-tool:latest
          restartPolicy: OnFailure
```

### StatefulSets

```bash
# List StatefulSets
kubectl get statefulsets
kubectl get sts                    # Short form

# Scale StatefulSet
kubectl scale statefulset <name> --replicas=3
```

### DaemonSets

```bash
# List DaemonSets
kubectl get daemonsets
kubectl get ds                     # Short form

# Describe DaemonSet
kubectl describe daemonset <name>
```

### Ingress

```bash
# List ingress
kubectl get ingress
kubectl get ing                    # Short form

# Describe ingress
kubectl describe ingress <name>
```

```yaml
# Ingress example
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
```

---

## 🎭 Act X: Useful One-Liners

```bash
# Get all resources in namespace
kubectl get all -n <namespace>

# Delete all pods in namespace
kubectl delete pods --all -n <namespace>

# Get pods with labels
kubectl get pods -l app=nginx

# Watch resources
kubectl get pods -w
kubectl get pods -w -n <namespace>

# Output as YAML/JSON
kubectl get pod <name> -o yaml
kubectl get pod <name> -o json

# Dry run
kubectl run nginx --image=nginx --dry-run=client -o yaml
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml

# Generate YAML from command
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml

# Edit resource
kubectl edit pod <pod-name>
kubectl edit deployment <deployment-name>

# Patch resource
kubectl patch deployment <name> -p '{"spec":{"replicas":5}}'

# Label resources
kubectl label pod <name> env=production
kubectl label pod <name> env=production --overwrite

# Annotate resources
kubectl annotate pod <name> description="My pod"
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Force delete pod
kubectl delete pod <name> --grace-period=0 --force

# Drain node (for maintenance)
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data

# Uncordon node (bring it back)
kubectl uncordon <node-name>

# Cordon node (prevent scheduling)
kubectl cordon <node-name>

# Get cluster info for debugging
kubectl cluster-info dump

# Check API server status
kubectl get --raw /healthz

# Restart deployment (force new pods)
kubectl rollout restart deployment/<name>

# Delete everything in namespace (nuclear option)
kubectl delete all --all -n <namespace>
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| List pods | `kubectl get pods` |
| Describe pod | `kubectl describe pod <name>` |
| Pod logs | `kubectl logs <name>` |
| Exec into pod | `kubectl exec -it <name> -- /bin/bash` |
| Create deployment | `kubectl create deployment <name> --image=<img>` |
| Scale deployment | `kubectl scale deployment <name> --replicas=3` |
| Port forward | `kubectl port-forward <pod> 8080:80` |
| Apply YAML | `kubectl apply -f file.yaml` |
| Delete resource | `kubectl delete <type> <name>` |
| Get all | `kubectl get all` |
| Watch resources | `kubectl get pods -w` |

---

## 🎬 The End Credits

Remember:
- `kubectl get` is your friend
- `kubectl describe` shows you why things are broken
- `kubectl logs` is where the truth lives
- Always check events: `kubectl get events`
- YAML indentation matters (a lot)
- Namespaces are your organizational friend
- Secrets should stay secret (don't commit them!)

**Now go orchestrate something awesome!** ☸️

---

*Made with ☕ and the tears of developers who forgot to set resource limits*

*Last updated: When you finally remember the kubectl command you needed*

