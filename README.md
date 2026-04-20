# 🦁 CleanStart Tigera Operator

Tigera Operator is the Kubernetes operator that manages Calico networking and network policies. It automates the deployment, configuration, and lifecycle management of Calico components in Kubernetes clusters, providing network policy enforcement, pod-to-pod networking, and network security capabilities. The CleanStart Tigera-Operator image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `ghcr.io/cleanstart-containers/tigera-operator`

**Registry:** CleanStart Registry

---

## Overview

Tigera Operator is a Kubernetes-native operator that manages Calico networking and network policies. It operates as a cluster-scoped controller, continuously monitoring and managing Calico components across the Kubernetes cluster. This CleanStart Tigera-Operator container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## Image Details

**Image:** `ghcr.io/cleanstart-containers/tigera-operator:latest-dev`

**Key Specifications:**
- **User:** `clnstrt` (non-root, UID 1000)
- **Architecture:** `amd64`
- **OS:** `linux`
- **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- Kubernetes-native operator for Calico networking
- Automated Calico component deployment and management
- Network policy enforcement and management
- Pod-to-pod networking configuration
- CNI (Container Network Interface) management
- Security-hardened base image with minimal attack surface
- Non-root execution for enhanced security

---

## Common Use Cases

Typical scenarios where this container excels:

- Network policy management in Kubernetes
- CNI deployment and configuration
- Network security and segmentation
- Multi-tenant networking isolation
- Development and testing of Calico/Tigera functionality
- CI/CD integration for automated Calico deployment
- Enterprise networking automation
- Zero-trust network architecture implementation

---

## Quick Start

### Pull Commands
```bash
docker pull ghcr.io/cleanstart-containers/tigera-operator:latest
docker pull ghcr.io/cleanstart-containers/tigera-operator:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name tigera-operator-test ghcr.io/cleanstart-containers/tigera-operator:latest-dev
```

Production deployment:
```bash
docker run -d --name tigera-operator-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  ghcr.io/cleanstart-containers/tigera-operator:latest
```

---

## Kubernetes Deployment

The `kubernetes/` directory contains a complete Kubernetes deployment:

### Files

- `deployment.yaml` - Complete deployment manifest including:
  - Namespace (`tigera-operator`)
  - ServiceAccount
  - ClusterRole and ClusterRoleBinding (RBAC)
  - Deployment with proper security context
  - Service for metrics and webhook endpoints
- `README.md` - Comprehensive deployment guide with:
  - Step-by-step installation instructions
  - Prerequisites and requirements
  - Testing procedures
  - Troubleshooting guide
  - Configuration details

---

## Configuration

### Resource Limits

- **Requests:** CPU: 100m, Memory: 128Mi
- **Limits:** CPU: 500m, Memory: 512Mi

### Security Context

- **Run as non-root:** `runAsUser: 1000`
- **No privilege escalation:** `allowPrivilegeEscalation: false`
- **Drop all capabilities:** Security hardened

---

## Best Practices

- Use specific image tags for production (avoid `latest-dev` in production)
- Configure resource limits: CPU and memory constraints
- Monitor operator logs for controller startup and errors
- Keep Calico/Tigera CRDs up to date
- Use RBAC with least privilege principle
- Note: The `installations.operator.tigera.io` CRD may not install due to Kubernetes annotation size limits - this is expected

---

## Security Notes

The container runs with multiple security enhancements:

- The container runs as non-root user (`clnstrt`, UID 1000)
- All capabilities are dropped for security
- RBAC is configured with least privilege principle
- SSL certificates are pre-configured for secure communication
- Leader election ensures only one instance is active at a time

---

## Testing

The Kubernetes deployment includes comprehensive testing procedures:

- Pod verification tests
- Operator log checks
- RBAC permission verification
- Service endpoint validation
- Resource usage monitoring

---

## Troubleshooting

Common issues and solutions:

- Pod startup issues
- CRD installation errors
- Deployment selector immutability errors
- Installation CRD errors (expected behavior)

For detailed troubleshooting guidance, refer to the `kubernetes/README.md` file.

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Architecture-based Pull Commands
```bash
docker pull --platform linux/amd64 ghcr.io/cleanstart-containers/tigera-operator:latest
docker pull --platform linux/arm64 ghcr.io/cleanstart-containers/tigera-operator:latest
```

---

## Documentation Resources
Essential links and resources for further information
 
**CleanStart Images**: https://images.cleanstart.com/
 
**Community Images**:<br>
**Docker Hub**: https://hub.docker.com/u/cleanstart<br>
**GitHub**: https://github.com/cleanstart-containers<br>
**AWS ECR Public Gallery**: https://gallery.ecr.aws/cleanstart/
 
**Presence on Social Media**:<br>
**Community**: https://www.linkedin.com/groups/18324021/<br>
**YouTube**: https://www.youtube.com/@CleanStartOfficial<br>
 
**Contribute to Container Use Cases**: https://github.com/cleanstart-dev/cleanstart-use-cases/

## Disclaimer & License

### Disclaimer

**Disclaimer:** This documentation is provided for informational purposes only. Users are responsible for ensuring compliance with applicable laws, regulations, and security requirements. CleanStart makes no warranties regarding the suitability of these images for specific use cases or environments.

### License

Apache-2.0

---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.
