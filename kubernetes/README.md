# 🦁 Tigera Operator - Kubernetes Deployment Guide

Kubernetes deployment guide for the CleanStart Tigera Operator container.

## 📁 Files

- `deployment.yaml` - Complete deployment manifest (Namespace, ServiceAccount, RBAC, Deployment, Service)
- `README.md` - This documentation

## 🖼️ Image Details

**Image:** `cleanstart/tigera-operator:latest-dev`

**Key Features:**
- **User:** `clnstrt` (non-root, UID 1000)
- **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

**Note:** This deployment uses the official Tigera Operator from Step 1. The CleanStart image deployment is configured to work alongside it.

## 🚀 Deployment Steps

### Prerequisites

- Kubernetes cluster (Kind, minikube, k3s, GKE, EKS, AKS, or any other)
- `kubectl` configured to access your cluster
- RBAC permissions to create namespaces, deployments, services, service accounts, cluster roles, and cluster role bindings

### Step 1: Install Calico/Tigera CRDs

```bash
# Install Tigera Operator CRDs
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/tigera-operator.yaml
```

**CRD Installation Note:** You may see an error for `installations.operator.tigera.io` due to Kubernetes annotation size limits. This is expected and doesn't prevent other CRDs from working.

### Step 2: Deploy CleanStart Image

```bash
# Navigate to the deployment directory
cd containers/tigera-operator/kubernetes

# Delete the deployment that came during crd's installation and apply our the deployment
kubectl delete deployment tigera-operator -n tigera-operator && kubectl apply -f deployment.yaml
```

**Expected Output:**
```
namespace/tigera-operator configured
serviceaccount/tigera-operator configured
clusterrole.rbac.authorization.k8s.io/tigera-operator configured
clusterrolebinding.rbac.authorization.k8s.io/tigera-operator configured
deployment.apps/tigera-operator created
service/tigera-operator created
```

### Step 3: Verify Deployment

```bash
# Check pod status
kubectl get pods -n tigera-operator

# View logs
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')
kubectl logs -n tigera-operator $POD_NAME
```

**Expected Status:** Pod should be in `Running` state with `1/1 Ready`.

**Expected Log Output:**
```
2025/11/10 18:22:14 [INFO] Version: v1.32.3
2025/11/10 18:22:14 [INFO] Go Version: go1.21.5 X:boringcrypto
2025/11/10 18:22:15 [INFO] Active operator: proceeding
{"level":"info","ts":"2025-11-10T18:22:15Z","logger":"setup","msg":"starting manager"}
I1110 18:22:16.123246       1 leaderelection.go:258] successfully acquired lease tigera-operator/operator-lock
{"level":"info","ts":"2025-11-10T18:22:16Z","msg":"Starting Controller","controller":"tigera-installation-controller"}
```

**Note:** You may see errors about `Installation.operator.tigera.io` CRD not being found. This is expected if the CRD wasn't installed due to annotation size limits.

## 🧪 Testing

### Test 1: Verify Pod is Running

```bash
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')
kubectl get pod $POD_NAME -n tigera-operator
kubectl logs $POD_NAME -n tigera-operator
```

**Expected Status:** Pod should be `1/1 Running` with operator controllers started.

### Test 2: Check Operator Logs

```bash
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')

# Check operator logs for successful startup
kubectl logs -n tigera-operator $POD_NAME | grep -i "active operator\|successfully acquired\|starting controller"

# Check for errors
kubectl logs -n tigera-operator $POD_NAME | grep -i error | head -5
```

**Expected Output:** Should show "Active operator: proceeding" and may show "successfully acquired lease" and "Starting Controller" messages. Installation CRD errors are expected if the CRD wasn't installed.

### Test 3: Verify RBAC Permissions

```bash
kubectl auth can-i get tigerastatuses --as=system:serviceaccount:tigera-operator:tigera-operator --all-namespaces
kubectl auth can-i get networkpolicies --as=system:serviceaccount:tigera-operator:tigera-operator --all-namespaces
kubectl auth can-i get leases --as=system:serviceaccount:tigera-operator:tigera-operator -n tigera-operator
kubectl auth can-i create deployments --as=system:serviceaccount:tigera-operator:tigera-operator --all-namespaces
```

**Expected Output:** All commands should return `yes`.

### Test 4: Verify Service

```bash
# Get pod name first
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')

# Check service and endpoints
kubectl get endpoints -n tigera-operator tigera-operator
kubectl get service -n tigera-operator tigera-operator

# Check pod (use direct pod name, not label selector)
kubectl get pods -n tigera-operator
```

**Expected Output:** 
- Service should exist with ports 8080 and 9443
- Endpoints may show `<none>` if pod labels don't match service selector
- Pod should be visible when listing all pods in the namespace

### Test 5: Check Resource Usage

```bash
# Get pod name first
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')

# Check resource usage (use direct pod name)
kubectl top pod -n tigera-operator $POD_NAME
```

**Expected Output:** 
- CPU usage typically 5-10m, Memory usage typically 20-30Mi
- Resource limits are configured in deployment.yaml: CPU: 100m-500m, Memory: 128Mi-512Mi
- Example output: `NAME CPU(cores) MEMORY(bytes) tigera-operator-xxxxx 7m 28Mi`

## 🔧 Configuration

### Resource Limits

- **Requests:** CPU: 100m, Memory: 128Mi
- **Limits:** CPU: 500m, Memory: 512Mi

### Security Context

- **Run as non-root:** `runAsUser: 1000`
- **No privilege escalation:** `allowPrivilegeEscalation: false`
- **Drop all capabilities:** Security hardened

### Ports

- **8080:** Metrics endpoint
- **9443:** Webhook endpoint

## 🔍 Troubleshooting

### Pod Not Starting / CrashLoopBackOff

```bash
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')
kubectl describe pod -n tigera-operator $POD_NAME | grep -A 10 "Last State\|State\|Message\|Error"
kubectl logs -n tigera-operator $POD_NAME
```

### Installation CRD Errors

If you see errors like: `no matches for kind "Installation" in version "operator.tigera.io/v1"`

**Note:** This is expected if the `installations.operator.tigera.io` CRD wasn't installed due to Kubernetes annotation size limits. The operator will continue running but won't be able to manage Installation resources.

### No Pods Found with Label Selector

```bash
kubectl get pods -n tigera-operator
POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')
kubectl logs -n tigera-operator $POD_NAME
```

## 🧹 Cleanup

```bash
# Delete all resources
kubectl delete -f deployment.yaml

# Or delete individually
kubectl delete deployment tigera-operator -n tigera-operator
kubectl delete service tigera-operator -n tigera-operator
kubectl delete serviceaccount tigera-operator -n tigera-operator
kubectl delete clusterrole tigera-operator
kubectl delete clusterrolebinding tigera-operator
kubectl delete namespace tigera-operator
```

**Note:** CRDs installed separately will remain unless explicitly deleted.

## ✅ Verification Checklist

- [ ] Pod is running (`kubectl get pods -n tigera-operator`) - Should show `1/1 Running`
- [ ] Pod logs show "Active operator: proceeding" and operator startup messages
- [ ] Operator controllers are starting (tigera-installation-controller, apiserver-controller, etc.)
- [ ] Service exists with ports 8080 and 9443
- [ ] RBAC permissions are correct (all test commands return `yes`)
- [ ] Leader election is working (lease acquired)
- [ ] Resource usage is within limits
- [ ] Container stays running (no CrashLoopBackOff)
- [ ] Installation CRD errors are expected (CRD may not be installed due to annotation size limits)

## 📝 Next Steps

After verifying the operator is running:

1. **Monitor Operator Logs:**
   ```bash
   POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')
   kubectl logs -n tigera-operator $POD_NAME -f
   ```

2. **Check Operator Status:**
   ```bash
   kubectl get tigerastatus
   kubectl get installation default -o yaml 2>/dev/null || echo "Installation CRD not available"
   ```

**Expected Output:**
- `kubectl get tigerastatus` may return "No resources found" (no status resources created yet)
- `kubectl get installation default` will return "Installation CRD not available" (CRD not installed due to annotation size limits)

3. **View Running Controllers:**
   ```bash
   POD_NAME=$(kubectl get pods -n tigera-operator -o jsonpath='{.items[0].metadata.name}')
   kubectl logs -n tigera-operator $POD_NAME | grep -i "controller\|starting"
   ```

**Note:** The operator is running and managing Calico/Tigera resources. Installation CRD errors are expected if the CRD wasn't installed due to annotation size limits. Some commands may return empty results if resources haven't been created yet.
