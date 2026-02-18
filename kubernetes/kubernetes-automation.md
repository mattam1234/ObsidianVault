# Kubernetes Automation

**Kubernetes Automation** encompasses tools, practices, and workflows for automating Kubernetes deployments, configurations, and operations.

---

## Automation Categories

### 1. Infrastructure Automation
- Cluster provisioning and management
- Node lifecycle management
- Network and storage configuration

### 2. Application Deployment Automation
- Continuous Deployment (CD)
- GitOps workflows
- Release management

### 3. Configuration Management
- ConfigMap and Secret management
- Application configuration
- Environment-specific configs

### 4. Operational Automation
- Auto-scaling
- Self-healing
- Backup and disaster recovery
- Monitoring and alerting

---

## GitOps

**GitOps** is a declarative approach where Git is the single source of truth for infrastructure and applications.

### ArgoCD

**Install ArgoCD:**
```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get initial password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

**Create Application:**
```yaml
# application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/myorg/myapp
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Flux

**Install Flux:**
```bash
# Install Flux CLI
curl -s https://fluxcd.io/install.sh | sudo bash

# Bootstrap Flux
flux bootstrap github \
  --owner=myorg \
  --repository=fleet-infra \
  --path=clusters/production \
  --personal
```

**Create GitRepository:**
```yaml
# gitrepository.yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: myapp
  namespace: flux-system
spec:
  interval: 1m
  url: https://github.com/myorg/myapp
  ref:
    branch: main
```

---

## CI/CD Pipelines

### GitHub Actions

**Deploy to Kubernetes:**
```yaml
# .github/workflows/deploy.yml
name: Deploy to Kubernetes

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up kubectl
        uses: azure/setup-kubectl@v3
        
      - name: Configure Kubernetes
        run: |
          echo "${{ secrets.KUBE_CONFIG }}" | base64 -d > kubeconfig
          export KUBECONFIG=kubeconfig
          
      - name: Deploy
        run: |
          kubectl apply -f k8s/
          kubectl rollout status deployment/myapp
```

### GitLab CI/CD

**Deploy Pipeline:**
```yaml
# .gitlab-ci.yml
stages:
  - build
  - deploy

deploy:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config use-context $KUBE_CONTEXT
    - kubectl apply -f k8s/
    - kubectl rollout status deployment/myapp
  only:
    - main
```

### Jenkins

**Kubernetes Plugin:**
```groovy
// Jenkinsfile
pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bitnami/kubectl
    command: ['cat']
    tty: true
'''
        }
    }
    stages {
        stage('Deploy') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f k8s/'
                    sh 'kubectl rollout status deployment/myapp'
                }
            }
        }
    }
}
```

---

## Helm Automation

**Helm Chart Deployment:**
```bash
# Install chart
helm install myapp ./mychart

# Upgrade chart
helm upgrade myapp ./mychart

# Automated rollback on failure
helm upgrade --install --atomic --timeout 5m myapp ./mychart
```

**Helm with CI/CD:**
```yaml
# .github/workflows/helm.yml
- name: Deploy with Helm
  run: |
    helm upgrade --install myapp ./chart \
      --set image.tag=${{ github.sha }} \
      --wait --timeout 5m
```

**Helmfile for Multiple Charts:**
```yaml
# helmfile.yaml
repositories:
  - name: stable
    url: https://charts.helm.sh/stable

releases:
  - name: nginx-ingress
    namespace: ingress
    chart: stable/nginx-ingress
    values:
      - values/nginx-ingress.yaml
      
  - name: myapp
    namespace: production
    chart: ./charts/myapp
    values:
      - values/myapp.yaml
```

---

## Infrastructure as Code

### Terraform

**Provision EKS Cluster:**
```hcl
# main.tf
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 19.0"

  cluster_name    = "my-cluster"
  cluster_version = "1.28"

  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets

  eks_managed_node_groups = {
    default = {
      min_size     = 2
      max_size     = 4
      desired_size = 2
      instance_types = ["t3.medium"]
    }
  }
}
```

### Pulumi

**Deploy Kubernetes Resources:**
```python
# __main__.py
import pulumi
from pulumi_kubernetes.apps.v1 import Deployment, DeploymentSpecArgs
from pulumi_kubernetes.core.v1 import ContainerArgs, PodSpecArgs, PodTemplateSpecArgs

app_labels = {"app": "nginx"}

deployment = Deployment(
    "nginx",
    spec=DeploymentSpecArgs(
        selector={"matchLabels": app_labels},
        replicas=2,
        template=PodTemplateSpecArgs(
            metadata={"labels": app_labels},
            spec=PodSpecArgs(
                containers=[ContainerArgs(
                    name="nginx",
                    image="nginx:alpine"
                )]
            )
        )
    )
)
```

---

## Auto-Scaling

### Horizontal Pod Autoscaler (HPA)

```yaml
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

### Vertical Pod Autoscaler (VPA)

```yaml
# vpa.yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  updatePolicy:
    updateMode: "Auto"
```

### Cluster Autoscaler

**On AWS:**
```yaml
# cluster-autoscaler.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
spec:
  template:
    spec:
      containers:
      - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.28.0
        name: cluster-autoscaler
        command:
          - ./cluster-autoscaler
          - --cloud-provider=aws
          - --nodes=2:10:my-node-group
```

---

## Configuration Management

### Kustomize

**Base Configuration:**
```yaml
# base/kustomization.yaml
resources:
  - deployment.yaml
  - service.yaml

configMapGenerator:
  - name: app-config
    files:
      - config.properties
```

**Overlays for Environments:**
```yaml
# overlays/production/kustomization.yaml
bases:
  - ../../base

patchesStrategicMerge:
  - replica-count.yaml
  - resources.yaml

images:
  - name: myapp
    newTag: v1.2.3
```

**Deploy:**
```bash
kubectl apply -k overlays/production/
```

### Sealed Secrets

**Install Sealed Secrets:**
```bash
# Install controller
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.24.0/controller.yaml

# Install kubeseal CLI
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.24.0/kubeseal-linux-amd64
sudo install -m 755 kubeseal-linux-amd64 /usr/local/bin/kubeseal
```

**Create Sealed Secret:**
```bash
# Create secret
kubectl create secret generic mysecret \
  --from-literal=password=secretvalue \
  --dry-run=client -o yaml | \
  kubeseal -o yaml > sealed-secret.yaml

# Apply sealed secret
kubectl apply -f sealed-secret.yaml
```

---

## Operators

**Create Custom Operator:**
```bash
# Install Operator SDK
brew install operator-sdk

# Create new operator
operator-sdk init --domain=example.com --repo=github.com/myorg/myoperator
operator-sdk create api --group=app --version=v1 --kind=MyApp
```

**Example Operator:**
```yaml
# myapp-operator.yaml
apiVersion: app.example.com/v1
kind: MyApp
metadata:
  name: myapp-instance
spec:
  size: 3
  image: myapp:latest
  database:
    enabled: true
```

---

## Backup and Disaster Recovery

### Velero

**Install Velero:**
```bash
# Install Velero
velero install \
  --provider aws \
  --plugins velero/velero-plugin-for-aws:v1.8.0 \
  --bucket my-backup-bucket \
  --secret-file ./credentials-velero

# Create backup
velero backup create my-backup

# Schedule backups
velero schedule create daily-backup --schedule="0 2 * * *"

# Restore from backup
velero restore create --from-backup my-backup
```

---

## Monitoring Automation

**Deploy Prometheus with Operator:**
```bash
# Install Prometheus Operator
kubectl apply -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/main/bundle.yaml

# Create ServiceMonitor
kubectl apply -f - <<EOF
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-monitor
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: metrics
    interval: 30s
EOF
```

---

## Policy Automation

### OPA Gatekeeper

**Install Gatekeeper:**
```bash
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
```

**Create Policy:**
```yaml
# constraint-template.yaml
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate
metadata:
  name: k8srequiredlabels
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredLabels
      validation:
        openAPIV3Schema:
          properties:
            labels:
              type: array
              items: string
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8srequiredlabels
        violation[{"msg": msg}] {
          not input.review.object.metadata.labels.app
          msg := "App label is required"
        }
```

---

## Related Topics

- [[kubernetes/kubernetes|Kubernetes]] - Container orchestration
- [[kubernetes/kubectl|kubectl]] - Kubernetes CLI
- [[tools/helm|Helm]] - Package manager
- [[tools/terraform|Terraform]] - Infrastructure as Code
- [[tools/ansible|Ansible]] - Configuration management

---

## Best Practices

✅ Use GitOps for declarative deployments
✅ Automate everything that can be automated
✅ Implement CI/CD pipelines
✅ Use Helm or Kustomize for templating
✅ Enable auto-scaling where appropriate
✅ Automate backups and disaster recovery
✅ Implement policy enforcement
✅ Monitor and alert on automation failures
✅ Version control all configurations
✅ Test automation in non-production first
✅ Document automation workflows
✅ Use operators for complex applications