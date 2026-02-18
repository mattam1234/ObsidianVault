# K3s Single Node Installation

This guide covers installing **K3s** on a single node for development, testing, or small production deployments.

For other installation methods, see:
- [[kubernetes/k3s-install-ha-embeddeddb|K3s HA with Embedded DB]]
- [[kubernetes/k3s-install-ha-externaldb|K3s HA with External DB]]

Documentation: [K3s Quick-Start Guide](https://docs.k3s.io/quick-start)

---

## Prerequisites

**System Requirements:**
- Linux OS (Ubuntu, Debian, RHEL, etc.)
- 1 CPU core (2+ recommended)
- 512 MB RAM (1 GB+ recommended)
- 1 GB disk space

**Supported Architectures:**
- x86_64 (amd64)
- ARM64 (aarch64)
- ARMv7 (armhf)

**Open Ports:**
- 6443: Kubernetes API server
- 10250: Kubelet metrics
- Optional: 80/443 for Ingress

---

## Quick Install

**One-Line Installation:**
```bash
curl -sfL https://get.k3s.io | sh -
```

This will:
1. Download and install K3s
2. Start K3s as a systemd service
3. Configure kubectl with admin credentials
4. Install Traefik ingress controller (by default)

**Verify Installation:**
```bash
# Check service status
sudo systemctl status k3s

# Check nodes
sudo k3s kubectl get nodes

# Check pods
sudo k3s kubectl get pods -A
```

---

## Custom Installation Options

### Install Specific Version

```bash
# Install specific version
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.28.3+k3s1 sh -

# Install latest stable
curl -sfL https://get.k3s.io | INSTALL_K3S_CHANNEL=stable sh -
```

### Install Without Traefik

```bash
# Disable Traefik (to use your own ingress)
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--disable=traefik" sh -
```

### Install Without ServiceLB

```bash
# Disable ServiceLB (to use MetalLB or other)
curl -sfL https://get.k3s.io | sh -s - --disable=servicelb
```

### Install with Custom Configuration

```bash
# Create config file
sudo mkdir -p /etc/rancher/k3s
sudo tee /etc/rancher/k3s/config.yaml <<EOF
write-kubeconfig-mode: "0644"
tls-san:
  - "my-k3s.example.com"
disable:
  - traefik
  - servicelb
EOF

# Install with config
curl -sfL https://get.k3s.io | sh -
```

---

## Post-Installation Configuration

### Access Kubeconfig

**As Root:**
```bash
# Kubeconfig is at /etc/rancher/k3s/k3s.yaml
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get nodes
```

**As Regular User:**
```bash
# Copy kubeconfig to user directory
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
chmod 600 ~/.kube/config

# Now use kubectl as regular user
kubectl get nodes
```

**Use k3s kubectl Wrapper:**
```bash
# Use k3s kubectl (runs as root)
sudo k3s kubectl get nodes

# Create alias
echo "alias kubectl='sudo k3s kubectl'" >> ~/.bashrc
source ~/.bashrc
```

### Install kubectl (Optional)

```bash
# Install kubectl separately
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Verify
kubectl version --client
```

---

## Configure Ingress

### Using Traefik (Default)

**Create Ingress:**
```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: web
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

**Apply:**
```bash
kubectl apply -f ingress.yaml
```

### Install Nginx Ingress (Alternative)

```bash
# Disable Traefik first (if needed)
# Reinstall: curl -sfL https://get.k3s.io | sh -s - --disable=traefik

# Install nginx ingress
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml
```

---

## Storage

### Local Path Provisioner (Default)

K3s includes local-path-provisioner by default:

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-path
  resources:
    requests:
      storage: 1Gi
```

**Default Storage Location:**
```
/var/lib/rancher/k3s/storage/
```

### Install Longhorn (Distributed Storage)

```bash
# Install Longhorn
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.5.1/deploy/longhorn.yaml

# Check installation
kubectl get pods -n longhorn-system
```

---

## Deploy Sample Application

**Create Deployment:**
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-demo
spec:
  replicas: 2
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
        image: nginx:alpine
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

**Deploy:**
```bash
kubectl apply -f deployment.yaml

# Check deployment
kubectl get deployments
kubectl get pods
kubectl get services
```

---

## Managing K3s

### Service Management

```bash
# Start K3s
sudo systemctl start k3s

# Stop K3s
sudo systemctl stop k3s

# Restart K3s
sudo systemctl restart k3s

# Enable auto-start
sudo systemctl enable k3s

# Check status
sudo systemctl status k3s

# View logs
sudo journalctl -u k3s -f
```

### Upgrade K3s

```bash
# Check current version
k3s --version

# Upgrade to latest
curl -sfL https://get.k3s.io | sh -

# Upgrade to specific version
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.28.3+k3s1 sh -
```

### Uninstall K3s

```bash
# Uninstall K3s
/usr/local/bin/k3s-uninstall.sh

# This will:
# - Stop K3s service
# - Remove K3s files
# - Clean up network configurations
# - Remove containers
```

---

## Troubleshooting

### Check Cluster Health

```bash
# Check nodes
kubectl get nodes

# Check system pods
kubectl get pods -n kube-system

# Check all resources
kubectl get all -A

# Describe node
kubectl describe node
```

### View Logs

```bash
# K3s service logs
sudo journalctl -u k3s -f

# Pod logs
kubectl logs <pod-name>

# Previous container logs
kubectl logs <pod-name> --previous

# All pods in deployment
kubectl logs -l app=myapp
```

### Common Issues

**Pods in Pending State:**
```bash
# Check events
kubectl describe pod <pod-name>

# Check node resources
kubectl top nodes
kubectl describe node
```

**Network Issues:**
```bash
# Check CNI
kubectl get pods -n kube-system | grep -E 'coredns|flannel'

# Test DNS
kubectl run -it --rm debug --image=busybox --restart=Never -- nslookup kubernetes.default
```

**Permission Issues:**
```bash
# Fix kubeconfig permissions
sudo chmod 644 /etc/rancher/k3s/k3s.yaml

# Or copy to user directory
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```

---

## Configuration Files

**K3s Config:**
```
/etc/rancher/k3s/config.yaml
```

**Kubeconfig:**
```
/etc/rancher/k3s/k3s.yaml
```

**Data Directory:**
```
/var/lib/rancher/k3s/
```

**Manifests (auto-deploy):**
```
/var/lib/rancher/k3s/server/manifests/
```

---

## Security

### Secure kubeconfig

```bash
# Restrict permissions
sudo chmod 600 /etc/rancher/k3s/k3s.yaml

# Or in config.yaml
write-kubeconfig-mode: "0600"
```

### Enable Pod Security Standards

```yaml
# /etc/rancher/k3s/config.yaml
kube-apiserver-arg:
  - "admission-control-config-file=/etc/kubernetes/pss-config.yaml"
```

### Network Policies

```yaml
# deny-all.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

---

## Monitoring

### Install Metrics Server

```bash
# Install metrics-server
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# For K3s, disable TLS verification
kubectl patch deployment metrics-server -n kube-system --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'

# Check metrics
kubectl top nodes
kubectl top pods -A
```

### Install Prometheus (Optional)

See [[apps/prometheus|Prometheus]] for installation guide.

---

## Related Topics

- [[kubernetes/k3s|K3s]] - Lightweight Kubernetes overview
- [[kubernetes/kubectl|kubectl]] - Kubernetes CLI
- [[kubernetes/k9s|K9s]] - Kubernetes TUI
- [[kubernetes/kubernetes|Kubernetes]] - Full Kubernetes
- [[docker/docker|Docker]] - Container runtime

---

## Best Practices

✅ Use specific K3s versions in production
✅ Configure persistent storage early
✅ Set up monitoring and logging
✅ Secure kubeconfig file
✅ Regular backups of /var/lib/rancher/k3s
✅ Use namespaces for organization
✅ Set resource limits on pods
✅ Keep K3s updated
✅ Document custom configurations
✅ Test upgrades in non-production first

---

## Quick Reference

```bash
# Install K3s
curl -sfL https://get.k3s.io | sh -

# Check status
sudo systemctl status k3s

# Access cluster
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get nodes

# Upgrade K3s
curl -sfL https://get.k3s.io | sh -

# Uninstall K3s
/usr/local/bin/k3s-uninstall.sh
```