# Cloud Computing Security (Module 20)






# 🎯 Learning Objectives

## 1. Cloud Fundamentals

Understand:

- Core cloud computing concepts
- Cloud architecture
- Shared responsibility model
- Service and deployment models

---

## 2. Threat Identification

Learn to identify:

- Cloud-specific attack vectors
- Misconfigurations
- Identity-based attacks
- Data exposure risks

---

## 3. Container Technologies

Gain practical understanding of:

- Docker
- Kubernetes
- Container orchestration
- Container security risks

---

## 4. Security Controls

Apply:

- Cloud security best practices
- IAM principles
- Zero Trust architecture
- Logging and monitoring

---

## 5. Security Tools

Evaluate tools used for:

- Cloud posture management
- Container security
- Threat detection
- Compliance monitoring

---

# ☁️ Cloud Computing Fundamentals

Cloud computing provides computing resources as on-demand services over the internet.

---

## Core Characteristics of Cloud Computing

### 1. On-Demand Self-Service

Users can provision computing resources automatically without requiring interaction with service providers.

Examples:

- Creating virtual machines
- Provisioning storage
- Deploying databases

---

### 2. Broad Network Access

Cloud services are accessible over networks using standard mechanisms such as:

- Web browsers
- APIs
- Mobile devices
- Thin and thick clients

---

### 3. Resource Pooling

Cloud providers pool resources to serve multiple customers using a **multi-tenant architecture**.

Examples:

- Shared storage
- Shared CPU resources
- Shared networking infrastructure

---

### 4. Rapid Elasticity

Resources can scale up or down automatically based on demand.

Examples:

- Auto Scaling Groups
- Serverless Functions
- Dynamic Storage Allocation

---

### 5. Measured Service

Cloud providers monitor and charge customers based on resource usage.

Examples:

- CPU usage
- Storage consumption
- Network bandwidth
- API requests

---

# 🤝 Shared Responsibility Model

Cloud security responsibilities are shared between the **Cloud Service Provider (CSP)** and the **Customer**.

## Provider Responsibilities

Commonly responsible for:

- Physical security
- Hardware
- Networking infrastructure
- Hypervisors
- Data center operations

---

## Customer Responsibilities

Commonly responsible for:

- Identity management
- Data security
- Access controls
- Operating system configuration
- Application security

> The exact responsibility depends on the selected cloud service model.

---

# ☁️ Cloud Service Models

---

## IaaS (Infrastructure as a Service)

Infrastructure as a Service provides virtualized computing resources.

Examples:

- Amazon EC2
- Azure Virtual Machines
- Google Compute Engine

### Customer Manages

- Operating systems
- Applications
- Data
- Middleware

### Provider Manages

- Physical hardware
- Networking
- Storage infrastructure
- Virtualization layer

---

## PaaS (Platform as a Service)

Provides a development and deployment platform.

Examples:

- Azure App Service
- Google App Engine
- AWS Elastic Beanstalk

### Customer Manages

- Applications
- Data

### Provider Manages

- Runtime
- Middleware
- Operating systems
- Infrastructure

---

## SaaS (Software as a Service)

Provides fully managed applications.

Examples:

- Microsoft 365
- Gmail
- Salesforce

### Customer Manages

- User access
- Data configuration

### Provider Manages

Everything else.

---

# 🌐 Cloud Deployment Models

---

## 1. Public Cloud

Infrastructure is owned and managed by third-party providers.

### Advantages

- Cost effective
- Highly scalable
- Minimal maintenance

### Disadvantages

- Reduced control
- Larger attack surface

Examples:

- AWS
- Azure
- Google Cloud

---

## 2. Private Cloud

Dedicated infrastructure for a single organization.

### Advantages

- Greater control
- Enhanced security
- Regulatory compliance

### Disadvantages

- Higher cost
- Increased maintenance

---

## 3. Hybrid Cloud

Combination of public and private cloud environments.

### Advantages

- Flexibility
- Workload optimization
- Disaster recovery

### Challenges

- Complex management
- Security consistency issues

---

## 4. Community Cloud

Shared infrastructure among organizations with common requirements.

Examples:

- Government sectors
- Healthcare organizations

---

# 🏢 Major Cloud Providers

## Amazon Web Services (AWS)

Market leader offering extensive cloud services.

### Security Services

- IAM
- GuardDuty
- Security Hub
- Inspector
- Macie

---

## Microsoft Azure

Enterprise-focused cloud platform.

### Security Services

- Microsoft Defender for Cloud
- Azure Sentinel
- Azure AD
- Key Vault

---

## Google Cloud Platform (GCP)

Known for analytics, AI, and strong security capabilities.

### Security Services

- Chronicle
- Security Command Center
- Cloud IAM
- Cloud Armor

---

# 🐳 Containers, Docker & Kubernetes

Containerization has revolutionized application deployment.

---

# Containerization Fundamentals

Containers package applications and dependencies together.

Unlike Virtual Machines, containers share the host operating system kernel.

---

## Containers vs Virtual Machines

| Feature | Containers | Virtual Machines |
|----------|------------|------------------|
| OS Kernel | Shared | Separate |
| Size | Lightweight | Heavy |
| Startup | Seconds | Minutes |
| Resource Usage | Low | High |
| Isolation | Process Level | Hardware Level |

---

## Advantages of Containers

- Lightweight
- Fast deployment
- Portability
- Scalability
- Consistent environments

---

## Security Challenges

- Shared kernel risks
- Image vulnerabilities
- Misconfigurations
- Secrets exposure
- Container escape attacks

---

# Docker Security

Docker enables containerized application deployment.

---

## Common Docker Risks

### Privileged Containers

Containers running with excessive privileges may compromise the host.

---

### Exposed Docker Socket

Access to `/var/run/docker.sock` may allow full host compromise.

---

### Vulnerable Base Images

Using outdated or untrusted images introduces vulnerabilities.

---

## Docker Hardening Best Practices

- Use minimal images
- Avoid running containers as root
- Regularly scan images
- Restrict container capabilities
- Implement access controls
- Keep Docker updated

---

# Kubernetes Security

Kubernetes orchestrates containers at scale.

---

## Core Components

### Pods

Smallest deployable unit containing one or more containers.

### Nodes

Worker machines running pods.

### Cluster

Collection of nodes managed together.

---

## Kubernetes Security Risks

### Misconfigured RBAC

Excessive permissions may lead to privilege escalation.

---

### Exposed Dashboards

Public Kubernetes dashboards can expose sensitive resources.

---

### Over-Permissive Service Accounts

Attackers may abuse service account tokens.

---

### Unencrypted etcd Database

Sensitive cluster information may be exposed.

---

# ☁️ Cloud-Based Attack Vectors

---

## 1. Misconfigured Storage

Examples:

- Public AWS S3 Buckets
- Public Azure Blob Storage
- Public GCS Buckets

### Impact

- Data breaches
- Information disclosure
- Compliance violations

---

## 2. Credential Leakage

Common sources:

- GitHub repositories
- Source code
- Logs
- Configuration files

Examples:

- API Keys
- Access Tokens
- Secret Keys

### Impact

- Account compromise
- Resource abuse
- Lateral movement

---

## 3. Insecure APIs

Weak APIs frequently lead to cloud breaches.

### Common Issues

- Weak authentication
- Missing authorization
- Poor input validation
- Lack of rate limiting

### Impact

- Data manipulation
- Unauthorized actions
- Service disruption

---

## 4. Over-Privileged IAM

Granting excessive permissions violates least privilege principles.

### Impact

- Privilege escalation
- Complete cloud compromise
- Persistent attacker access

---

# 🌐 Cloud Network Security

---

## Virtual Private Cloud (VPC)

Logical isolation of cloud resources.

Features:

- Custom IP ranges
- Route tables
- Network segmentation

---

## Subnets

Subnets divide networks into smaller segments.

Benefits:

- Segmentation
- Traffic control
- Reduced attack surface

---

## Security Groups & ACLs

Virtual firewalls controlling network traffic.

### Security Groups

- Stateful
- Instance-level

### Network ACLs

- Stateless
- Subnet-level

---

# Zero Trust Security

Zero Trust assumes no implicit trust.

## Principles

- Verify explicitly
- Use least privilege
- Assume breach

---

# ⚔️ Cloud Attack Methodology (Defensive Perspective)

## 1. Reconnaissance

Attackers enumerate:

- Subdomains
- Cloud assets
- Storage buckets
- Certificates

### Detection

Monitor logs for unusual discovery activity.

---

## 2. Exploit Misconfigurations

Common targets:

- Open ports
- Weak credentials
- Exposed services

### Prevention

- Continuous scanning
- Automated hardening
- Secure baselines

---

## 3. Privilege Escalation

Attackers abuse:

- IAM permissions
- Service accounts
- Misconfigured roles

### Mitigation

- Least privilege
- Continuous monitoring
- Regular access reviews

---

# 🛡️ Cloud Security Controls

---

## Identity & Access Management (IAM)

Best Practices:

- Enable MFA
- Enforce least privilege
- Rotate credentials
- Conduct permission reviews

---

## Logging & Monitoring

Enable:

- Audit logs
- Centralized logging
- Real-time alerts
- Threat detection

Examples:

- AWS CloudTrail
- Azure Monitor
- GCP Cloud Logging

---

## Encryption & Data Protection

Protect data:

- At Rest
- In Transit

Implement:

- TLS
- KMS
- Key rotation

---

## Configuration Management

Use:

- Infrastructure as Code (IaC)
- Policy as Code
- Compliance automation

Examples:

- Terraform
- CloudFormation
- Azure ARM Templates

---

# 🔧 Essential Cloud Security Tools

## Cloud Security Posture Management (CSPM)

Continuously identifies:

- Misconfigurations
- Compliance violations
- Security gaps

Examples:

- Prisma Cloud
- Wiz
- Microsoft Defender for Cloud

---

## Container Security Tools

### Image Scanning

Detect vulnerabilities in container images.

Examples:

- Trivy
- Clair
- Grype

---

### Runtime Protection

Monitor container behavior during execution.

Examples:

- Falco
- Sysdig Secure

---

## Threat Detection Tools

Examples:

- SIEM Platforms
- Cloud-native threat detection tools
- Threat intelligence platforms

Purpose:

- Detect suspicious activities
- Investigate incidents
- Respond to attacks

---

## Compliance Monitoring

Automates compliance checks for frameworks such as:

- ISO 27001
- NIST
- PCI-DSS
- HIPAA
- CIS Benchmarks

---

# 📝 Key Takeaways

- Cloud security follows a **shared responsibility model**.
- Misconfigurations remain the leading cause of cloud breaches.
- IAM security is critical for preventing privilege escalation.
- Containers introduce new attack surfaces requiring dedicated security controls.
- Continuous monitoring and logging are essential.
- Zero Trust and least privilege significantly reduce risk.
- Security automation is crucial in modern cloud environments.
