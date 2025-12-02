# 🦁 CleanStart Tigera Operator

## Overview

Tigera Operator is the Kubernetes operator that manages Calico networking and network policies. It automates the deployment, configuration, and lifecycle management of Calico components in Kubernetes clusters, providing network policy enforcement, pod-to-pod networking, and network security capabilities.

**Key Features:**
* Kubernetes-native operator for Calico networking
* Automated Calico component deployment and management
* Network policy enforcement and management
* Pod-to-pod networking configuration
* CNI (Container Network Interface) management
* Security-hardened base image with minimal attack surface
* Non-root execution for enhanced security

**Common Use Cases:**
* Network policy management in Kubernetes
* CNI deployment and configuration
* Network security and segmentation
* Multi-tenant networking isolation
* Development and testing of Calico/Tigera functionality
* CI/CD integration for automated Calico deployment

## Image Details

**Image:** `cleanstart/tigera-operator:latest-dev`

**Key Specifications:**
* **User:** `clnstrt` (non-root, UID 1000)
* **Architecture:** `amd64`
* **OS:** `linux`
* **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

## Kubernetes Deployment

The `kubernetes/` directory contains a complete Kubernetes deployment:

**Files:**
* `deployment.yaml` - Complete deployment manifest including:
  - Namespace (`tigera-operator`)
  - ServiceAccount
  - ClusterRole and ClusterRoleBinding (RBAC)
  - Deployment with proper security context
  - Service for metrics and webhook endpoints
* `README.md` - Comprehensive deployment guide with:
  - Step-by-step installation instructions
  - Prerequisites and requirements
  - Testing procedures
  - Troubleshooting guide
  - Configuration details


## Configuration

### Resource Limits

* **Requests:** CPU: 100m, Memory: 128Mi
* **Limits:** CPU: 500m, Memory: 512Mi

### Security Context

* **Run as non-root:** `runAsUser: 1000`
* **No privilege escalation:** `allowPrivilegeEscalation: false`
* **Drop all capabilities:** Security hardened


## Best Practices

* Use specific image tags for production (avoid `latest-dev` in production)
* Configure resource limits: CPU and memory constraints
* Monitor operator logs for controller startup and errors
* Keep Calico/Tigera CRDs up to date
* Use RBAC with least privilege principle
* Note: The `installations.operator.tigera.io` CRD may not install due to Kubernetes annotation size limits - this is expected

## Security Notes

* The container runs as non-root user (`clnstrt`, UID 1000)
* All capabilities are dropped for security
* RBAC is configured with least privilege principle
* SSL certificates are pre-configured for secure communication
* Leader election ensures only one instance is active at a time

## Testing

The Kubernetes deployment includes comprehensive testing procedures.
* Pod verification tests
* Operator log checks
* RBAC permission verification
* Service endpoint validation
* Resource usage monitoring

## Troubleshooting

* Pod startup issues
* CRD installation errors
* Deployment selector immutability errors
* Installation CRD errors (expected behavior)
