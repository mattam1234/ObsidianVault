# Kubernetes

**Kubernetes** (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

Project Homepage: [Kubernetes](https://kubernetes.io/)
Documentation: [Kubernetes Documentation](https://kubernetes.io/docs/home/)

---

## What is Kubernetes?

Kubernetes provides a framework to run distributed systems resiliently. It takes care of:
- **Service discovery and load balancing** - Exposes containers using DNS or IP
- **Storage orchestration** - Automatically mounts storage systems
- **Automated rollouts and rollbacks** - Manages desired state for containers
- **Automatic bin packing** - Optimizes resource utilization
- **Self-healing** - Restarts, replaces, and reschedules containers
- **Secret and configuration management** - Manages sensitive information

---

## Core Components

### Control Plane Components

**kube-apiserver**
- Front-end for the Kubernetes control plane
- Exposes the Kubernetes API

**etcd**
- Consistent and highly-available key-value store
- Stores all cluster data

**kube-scheduler**
- Watches for newly created Pods
- Selects nodes for Pods to run on

**kube-controller-manager**
- Runs controller processes
- Includes Node, Job, EndpointSlice, and ServiceAccount controllers

**cloud-controller-manager**
- Embeds cloud-specific control logic
- Links cluster to cloud provider API

### Node Components

**kubelet**
- Ensures containers are running in a Pod
- Agent that runs on each node

**kube-proxy**
- Network proxy running on each node
- Maintains network rules for Pod communication

**Container Runtime**
- Software responsible for running containers
- Supports containerd, CRI-O, and any CRI implementation

---

## Key Concepts

### Pods
- Smallest deployable unit in Kubernetes
- Group of one or more containers
- Share storage and network resources

### Services
- Abstraction to expose applications
- Types: ClusterIP, NodePort, LoadBalancer, ExternalName

### Deployments
- Declarative updates for Pods and ReplicaSets
- Manages rolling updates and rollbacks

### Namespaces
- Virtual clusters within a physical cluster
- Isolate resources between multiple users

### ConfigMaps and Secrets
- ConfigMaps: Store non-confidential configuration
- Secrets: Store sensitive information

### Volumes
- Persistent storage for containers
- Outlives container lifecycle

---

## Basic kubectl Commands

**Cluster Info:**
```bash
kubectl cluster-info
kubectl get nodes
kubectl get componentstatuses
```

**Working with Pods:**
```bash
kubectl get pods
kubectl get pods -n <namespace>
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash
kubectl delete pod <pod-name>
```

**Working with Deployments:**
```bash
kubectl get deployments
kubectl create deployment <name> --image=<image>
kubectl scale deployment <name> --replicas=3
kubectl rollout status deployment/<name>
kubectl rollout undo deployment/<name>
```

**Working with Services:**
```bash
kubectl get services
kubectl expose deployment <name> --port=80 --type=NodePort
kubectl describe service <service-name>
```

**Apply Manifests:**
```bash
kubectl apply -f <file.yaml>
kubectl apply -f <directory>/
kubectl delete -f <file.yaml>
```

---

## Related Topics

- [[kubernetes/kubectl|kubectl]] - Kubernetes command-line tool
- [[kubernetes/k3s|K3s]] - Lightweight Kubernetes
- [[kubernetes/k9s|K9s]] - Kubernetes CLI manager
- [[kubernetes/kind|kind]] - Kubernetes in Docker
- [[docker/docker|Docker]] - Container platform

---

## Common Use Cases

1. **Microservices Architecture** - Deploy and manage microservices
2. **CI/CD Pipelines** - Automate application deployment
3. **Batch Processing** - Run batch jobs and parallel processing
4. **Machine Learning** - Deploy ML models at scale
5. **Hybrid Cloud** - Run workloads across multiple environments

---

## Best Practices

- Use namespaces to organize resources
- Define resource requests and limits
- Implement health checks (liveness, readiness probes)
- Use ConfigMaps and Secrets for configuration
- Version control your manifests
- Use labels and annotations effectively
- Implement network policies for security
- Regular backup of etcd data
- Monitor cluster health and application metrics