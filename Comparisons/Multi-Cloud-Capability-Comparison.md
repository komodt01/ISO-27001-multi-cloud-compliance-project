# Multi-Cloud Security Capability Comparison

## Purpose

This document compares representative AWS, Microsoft Azure, and Google Cloud services by **security and infrastructure capability**.

The services listed here should not be interpreted as exact one-to-one equivalents.

Each cloud provider organizes capabilities differently. A service in one platform may overlap with several services in another, and implementation details, scope, licensing, integration, and operating models can differ significantly.

For architecture decisions, I would start with the required capability and then determine how each cloud provides it.

---

## Compute

| Capability           | AWS              | Microsoft Azure                | Google Cloud                   |
| -------------------- | ---------------- | ------------------------------ | ------------------------------ |
| Virtual Machines     | Amazon EC2       | Azure Virtual Machines         | Compute Engine                 |
| Managed Kubernetes   | Amazon EKS       | Azure Kubernetes Service (AKS) | Google Kubernetes Engine (GKE) |
| Container Platform   | Amazon ECS / EKS | Azure Container Apps / AKS     | Cloud Run / GKE                |
| Serverless Functions | AWS Lambda       | Azure Functions                | Cloud Run functions            |
| Autoscaling Compute  | EC2 Auto Scaling | Virtual Machine Scale Sets     | Managed Instance Groups        |

Specific VM instance types are intentionally excluded because sizing and product families change and should be selected based on workload requirements rather than assumed to be direct equivalents.

---

## Storage

| Capability           | AWS                        | Microsoft Azure       | Google Cloud          |
| -------------------- | -------------------------- | --------------------- | --------------------- |
| Object Storage       | Amazon S3                  | Azure Blob Storage    | Cloud Storage         |
| Block Storage        | Amazon EBS                 | Azure Managed Disks   | Persistent Disk       |
| Managed File Storage | Amazon EFS / FSx           | Azure Files           | Filestore             |
| Archive Storage      | S3 Glacier storage classes | Azure Archive Storage | Cloud Storage Archive |

---

## Identity and Access

| Capability                   | AWS                              | Microsoft Azure               | Google Cloud                                    |
| ---------------------------- | -------------------------------- | ----------------------------- | ----------------------------------------------- |
| Cloud Resource Authorization | AWS IAM                          | Azure RBAC                    | Cloud IAM                                       |
| Workforce Identity           | IAM Identity Center / federation | Microsoft Entra ID            | Cloud Identity / Workforce Identity Federation  |
| Workload Identity            | IAM roles                        | Managed Identities            | Service Accounts / Workload Identity Federation |
| Secrets Management           | AWS Secrets Manager              | Azure Key Vault               | Secret Manager                                  |
| Key Management               | AWS KMS                          | Azure Key Vault / Managed HSM | Cloud KMS / Cloud HSM                           |

Identity models differ substantially across the three platforms.

For that reason, I would compare **trust relationships, authentication, authorization, workload identity, privilege management, and credential lifecycle** rather than simply matching service names.

---

## Data Discovery and Governance

| Capability                         | AWS                                                           | Microsoft Azure                                      | Google Cloud                                                   |
| ---------------------------------- | ------------------------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------- |
| Sensitive Data Discovery           | Amazon Macie for S3                                           | Microsoft Purview capabilities                       | Sensitive Data Protection                                      |
| Data Classification                | Macie findings and supporting governance processes            | Microsoft Purview classification                     | Sensitive Data Protection inspection                           |
| Data Governance                    | AWS services plus organizational governance tooling/processes | Microsoft Purview                                    | Dataplex Universal Catalog and related governance capabilities |
| De-identification / Transformation | Architecture-dependent services and application processing    | Service/application-dependent masking and protection | Sensitive Data Protection transformations                      |

These capabilities are **not direct equivalents**.

For example, Amazon Macie is strongly focused on discovering sensitive information in Amazon S3, while Microsoft Purview provides broader data-governance capabilities.

---

## Data Protection

| Capability                       | AWS                            | Microsoft Azure                                   | Google Cloud                                      |
| -------------------------------- | ------------------------------ | ------------------------------------------------- | ------------------------------------------------- |
| Encryption at Rest               | Service encryption + AWS KMS   | Service encryption + Key Vault                    | Default encryption + Cloud KMS                    |
| Customer-Managed Keys            | AWS KMS                        | Azure Key Vault / Managed HSM                     | Cloud KMS                                         |
| Database Masking / Protection    | Database/application-dependent | Azure SQL Dynamic Data Masking / Always Encrypted | BigQuery and application-level protection options |
| Tokenization / De-identification | Architecture-dependent         | Architecture-dependent                            | Sensitive Data Protection transformations         |
| Certificate Management           | AWS Certificate Manager        | Azure certificate-management capabilities         | Certificate Manager                               |

Encryption should be evaluated as an architecture rather than a checkbox.

Key ownership, access, rotation, separation of duties, logging, recovery, residency, and application dependencies all matter.

---

## Logging and Monitoring

| Capability                      | AWS                                   | Microsoft Azure                 | Google Cloud                                                      |
| ------------------------------- | ------------------------------------- | ------------------------------- | ----------------------------------------------------------------- |
| Administrative / Audit Activity | AWS CloudTrail                        | Azure Activity Log              | Cloud Audit Logs                                                  |
| Metrics                         | Amazon CloudWatch                     | Azure Monitor                   | Cloud Monitoring                                                  |
| Log Collection / Storage        | CloudWatch Logs and related services  | Azure Monitor / Log Analytics   | Cloud Logging                                                     |
| Log Query / Analysis            | CloudWatch Logs Insights              | Log Analytics / KQL             | Logs Explorer / Log Analytics capabilities                        |
| Alerting                        | CloudWatch Alarms                     | Azure Monitor Alerts            | Cloud Monitoring Alerting                                         |
| Security Posture / Findings     | Security Hub and related services     | Microsoft Defender for Cloud    | Security Command Center                                           |
| Threat Detection                | GuardDuty and other security services | Microsoft Defender capabilities | Security Command Center and related threat-detection capabilities |

A multi-cloud monitoring architecture should determine:

* Which telemetry is required
* Where it is retained
* Who can access it
* Which detections run locally
* Which events are forwarded to an enterprise SIEM
* How alerts are escalated
* How telemetry failure is detected

---

## Configuration and Governance

| Capability                         | AWS                                           | Microsoft Azure                                     | Google Cloud                               |
| ---------------------------------- | --------------------------------------------- | --------------------------------------------------- | ------------------------------------------ |
| Resource Configuration Assessment  | AWS Config                                    | Azure Policy / Resource Graph / related services    | Cloud Asset Inventory and related services |
| Preventive Organization Guardrails | Service Control Policies and related controls | Azure Policy / management-group governance          | Organization Policy                        |
| Resource Hierarchy                 | Organizations / OUs / Accounts                | Management Groups / Subscriptions / Resource Groups | Organization / Folders / Projects          |
| Infrastructure as Code             | CloudFormation / CDK / Terraform              | ARM / Bicep / Terraform                             | Terraform / Infrastructure Manager         |

These services should be compared by **control objective** rather than product name.

For example:

**Prevent prohibited configuration → Detect drift → Generate evidence → Remediate**

Each cloud implements those stages differently.

---

## Network Security

| Capability                   | AWS                     | Microsoft Azure                           | Google Cloud            |
| ---------------------------- | ----------------------- | ----------------------------------------- | ----------------------- |
| Virtual Network              | Amazon VPC              | Azure Virtual Network                     | VPC                     |
| Workload Traffic Filtering   | Security Groups / NACLs | Network Security Groups                   | VPC Firewall Rules      |
| Managed Firewall             | AWS Network Firewall    | Azure Firewall                            | Cloud NGFW              |
| Web Application Protection   | AWS WAF                 | Azure Web Application Firewall            | Cloud Armor             |
| Private Service Connectivity | AWS PrivateLink         | Azure Private Link                        | Private Service Connect |
| Load Balancing               | Elastic Load Balancing  | Azure Load Balancer / Application Gateway | Cloud Load Balancing    |
| CDN                          | Amazon CloudFront       | Azure Front Door / CDN capabilities       | Cloud CDN               |

Network architectures should be evaluated around trust boundaries, segmentation, ingress, egress, private connectivity, inspection, DNS, administrative access, and telemetry rather than service-name equivalency.

---

## Vulnerability and Security Posture

| Capability               | AWS                                          | Microsoft Azure                 | Google Cloud                                                 |
| ------------------------ | -------------------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| Vulnerability Assessment | Amazon Inspector                             | Microsoft Defender capabilities | Security Command Center capabilities and integrated services |
| Cloud Security Posture   | AWS Security Hub / Config / related services | Microsoft Defender for Cloud    | Security Command Center                                      |
| Threat Detection         | Amazon GuardDuty                             | Microsoft Defender capabilities | Security Command Center threat-detection capabilities        |

The scope of these platforms differs, so architecture decisions should evaluate coverage rather than assuming equivalent features.

---

## Resilience

| Capability              | AWS                    | Microsoft Azure                                         | Google Cloud                                    |
| ----------------------- | ---------------------- | ------------------------------------------------------- | ----------------------------------------------- |
| Multi-Zone Architecture | Availability Zones     | Availability Zones                                      | Zones                                           |
| Autoscaling             | EC2 Auto Scaling       | VM Scale Sets / Autoscale                               | Managed Instance Groups / Autoscaling           |
| Load Distribution       | Elastic Load Balancing | Azure Load Balancer / Application Gateway               | Cloud Load Balancing                            |
| Backup                  | AWS Backup             | Azure Backup                                            | Backup and DR services                          |
| Disaster Recovery       | Architecture-dependent | Azure Site Recovery and architecture-dependent patterns | Backup and DR / architecture-dependent patterns |

Resilience requirements should begin with business impact, availability targets, recovery objectives, dependencies, and failure scenarios.

---

## ISO 27001 Alignment

I would **not map individual cloud services directly to ISO 27001 controls as if deploying the service satisfies the control**.

Instead, I would use a capability-based approach.

| Security Objective          | Representative Cloud Capabilities                              |
| --------------------------- | -------------------------------------------------------------- |
| Information Classification  | Sensitive-data discovery, classification, metadata, governance |
| Identity and Access Control | IAM, federation, RBAC, workload identity, privileged access    |
| Cryptographic Protection    | KMS, HSM, service encryption, certificate management           |
| Logging and Monitoring      | Audit logs, telemetry, SIEM integration, alerting              |
| Network Security            | Segmentation, firewalls, private connectivity, WAF             |
| Vulnerability Management    | Assessment, posture management, remediation workflows          |
| Data Protection             | Encryption, masking, tokenization, DLP, retention              |
| Resilience                  | Availability zones, autoscaling, backup, recovery              |
| Configuration Governance    | Policies, guardrails, configuration assessment, IaC            |

The exact relationship to ISO/IEC 27001 should be determined from the organization's:

* Applicable version of the standard
* ISMS scope
* Risk assessment
* Statement of Applicability
* Policies
* Architecture
* Control design
* Evidence of control effectiveness

---

## Architecture Perspective

For a multi-cloud architecture, I would not ask:

**“What is the Azure equivalent of this AWS service?”**

I would first ask:

**“What security capability or control objective are we trying to achieve?”**

Then evaluate:

**Business Requirement → Security Objective → Required Capability → Cloud-Native Implementation → Evidence → Monitoring → Exceptions**

That avoids forcing AWS, Azure, and Google Cloud into artificial one-to-one mappings and provides a more defensible way to design security consistently across multiple cloud platforms.
