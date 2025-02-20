# Secure Containers

Securing containers is a multi-layered approach that involves best practices across the build, deployment, and runtime phases. Here are some of the most common ways to secure containers:

### **1. Image Security** 
Trusted registries, minimal images, K8s admission controller
- **Use Trusted Base Images**: Always use official, signed images from trusted sources.
  - use images from official registries (docker hub, AWS ECR public)
  - specific image version and avoid latest FROM python:3.10-alpine
- **Minimal Base Images**: Use minimal or distroless images to reduce the attack surface.
  - python:3.10-alpine
- **Regularly Scan Images**: Use tools like **Trivy**, **Clair**, **Anchore**, or **Docker Scout** to scan for vulnerabilities.
- **Implement Image Signing**: Use **Docker Content Trust (DCT)** or **Notary** to verify image integrity.
- **Enforce Image Policies**: Use **Admission Controllers** in Kubernetes (e.g., **OPA/Gatekeeper** or **Kyverno**) to enforce security policies.
  - K8s Admission controllers to trusted registries

### **2. Secure Container Runtime**
do not run docker as root, use read-only file system in k8s, do not run in privileged mode
- **Run as Non-Root**: Configure containers to run as non-root users using the `USER` directive in Dockerfiles.
- **Limit Capabilities**: Drop unnecessary Linux capabilities using the `securityContext` field in Kubernetes or `--cap-drop` in Docker.
- **Use Seccomp and AppArmor/SELinux**: Restrict system calls using **Seccomp profiles** and enforce security policies with **AppArmor** or **SELinux**.
- **Read-Only File Systems**: Set filesystems to **read-only** unless explicitly required.
- **Disable Privileged Mode**: Avoid running containers in privileged mode (`--privileged` flag in Docker).

### **3. Network Security**
Calico network policies, istio for mtls using envoy proxy , don't use host networks and ports - breaks the protection
- **Use Network Policies**: In Kubernetes, define **NetworkPolicies** (calico) to restrict traffic between pods.
- **Implement TLS Everywhere**: Encrypt internal and external traffic using **mutual TLS (mTLS)** via **Istio**, **Linkerd**, or **Cilium**.
- **Restrict External Exposure**: Minimize the use of `hostPort`, `hostNetwork`, or unnecessary open ports.

### **4. Supply Chain Security**
scan images with sonarqube
- **Sign and Verify Builds**: Use **Sigstore (Cosign)** or **TUF (The Update Framework)** for verifying software artifacts.
- **Use SBOMs (Software Bill of Materials)**: Maintain an SBOM using **Syft** or **Grype** to track dependencies.
- **Secure CI/CD Pipelines**: Scan artifacts in pipelines using **SonarQube, Snyk**, or **Checkov** before deployment.

### **5. Kubernetes Security**
RBAC
- **Use Pod Security Standards (PSS)**: Restrict pod permissions (e.g., no `hostPath`, no `privileged` containers).
- **Enable Role-Based Access Control (RBAC)**: Restrict Kubernetes API access using fine-grained roles.
- **Audit Logs and Monitoring**: Enable **Kubernetes Audit Logging** and use monitoring tools like **Falco, Prometheus, Grafana, and Datadog**.
- **Use Service Mesh for Security**: Implement **Istio**, **Consul**, or **Linkerd** for better security enforcement.

### **6. Runtime Security**
- **Use Container Security Tools**: Implement **Falco**, **Sysdig Secure**, or **NeuVector** to detect and respond to runtime anomalies.
- **Monitor Container Logs**: Use **ELK (Elasticsearch, Logstash, Kibana)**, **Fluentd**, or **Grafana Loki** for centralized logging.
- **Detect and Respond to Threats**: Use tools like **AWS GuardDuty**, **GCP Security Command Center**, or **Azure Defender for Kubernetes**.

### **7. Secrets Management**
Vault
- **Use Secret Managers**: Store sensitive data in **AWS Secrets Manager, HashiCorp Vault, CyberArk, Azure Key Vault**, or **Kubernetes Secrets**.
- **Avoid Hardcoded Secrets**: Use environment variables or external secrets instead of storing them in container images or code.

### **8. Patching and Updates**
- **Automate Updates**: Use **Kured (Kubernetes reboot daemon)** or **OS patch management tools** to keep base images updated.
- **Monitor Vulnerabilities**: Subscribe to CVE alerts and use tools like **Grype** or **AWS Inspector**.