# ISO 27001 Multi-Cloud Security Architecture

## Project Overview

This project explores how security requirements can be translated into cloud-specific security capabilities across AWS, Microsoft Azure, and Google Cloud.

The central architecture problem is not making all three cloud environments identical.

It is determining:

**What security outcome does the business require, and how should that outcome be implemented and demonstrated in each cloud?**

The repository includes cloud-specific configuration examples, security monitoring patterns, data protection and classification examples, resilience considerations, cross-cloud capability comparisons, and multi-cloud SIEM/SOAR architecture.

ISO/IEC 27001 provides part of the governance context, but deploying a cloud security service does not by itself satisfy an ISO control or establish compliance.

---

## Project Scope

This is a **portfolio architecture project** combining practical cloud configuration examples with architecture analysis.

Some modules contain CLI examples demonstrating specific cloud capabilities. Other documents describe architecture patterns and production considerations that extend beyond the lab implementation.

The project is intended to demonstrate:

* Multi-cloud security architecture thinking
* Business-driven security requirements
* Cloud-specific control implementation
* Security monitoring and detection
* Identity and access considerations
* Data discovery and protection
* Network security
* Resilience
* Cross-cloud capability analysis
* Compliance-aware architecture decisions

It is **not** a production reference architecture, formal ISO 27001 assessment, certification package, or complete enterprise ISMS implementation.

---

## Architecture Approach

For this project, I use the following decision flow:

**Business Requirement → Data / Workload Context → Risk → Security Objective → Required Capability → Cloud-Native Implementation → Monitoring / Evidence → Exception or Remediation**

This keeps architecture decisions tied to business and security requirements rather than beginning with individual cloud products.

---

## Multi-Cloud Design Principle

AWS, Azure, and Google Cloud provide many similar security capabilities, but they do not organize or implement those capabilities identically.

For that reason, I compare the platforms by **security capability** rather than assuming one-to-one service equivalency.

For example:

**Security requirement:** Protect sensitive information.

That requirement might involve different combinations of:

* Data discovery
* Classification
* Identity
* Encryption
* Key management
* Masking or de-identification
* Monitoring
* Retention

depending on the cloud platform, workload, data, and business requirement.

The objective is **consistent security outcomes, not identical cloud implementations**.

---

# Security Capabilities Explored

## 1. Identity and Access

The project considers identity as a foundational security capability across all three clouds.

Architecture considerations include:

* Authentication
* Authorization
* Least privilege
* Privileged access
* Workload identity
* Credential management
* Administrative activity
* Access governance

The environment prerequisite documents also distinguish basic CLI authentication from an enterprise identity architecture.

---

## 2. Data Discovery and Classification

Cloud-native discovery and classification capabilities explored include:

* Amazon Macie
* Microsoft Purview
* Google Cloud Sensitive Data Protection

The architecture does not treat discovery as the end of the security process.

The broader flow is:

**Discover → Classify → Validate → Determine Exposure → Assess Risk → Remediate → Monitor**

---

## 3. Data Protection

The project examines multiple forms of data protection, including:

* Encryption at rest
* Key management
* Data masking
* De-identification
* Access control
* Classification
* Monitoring

A key distinction is that these capabilities solve different security problems.

For example, encryption protects stored data from particular threats, while masking limits what an authorized user or application may see.

Neither should automatically be described as a complete Data Loss Prevention architecture.

---

## 4. Network Security

Network examples examine how cloud-native controls can restrict workload communication.

Architecture considerations include:

* Trust boundaries
* Segmentation
* Administrative access
* Ingress
* Egress
* Firewall policy
* Workload targeting
* Private connectivity
* Network telemetry

The requirement begins with the communication path:

**Who needs connectivity → From where → To what workload → Using what protocol → For what purpose**

The cloud-native firewall mechanism follows that requirement.

---

## 5. Logging, Monitoring, and Detection

The repository contains cloud-specific monitoring patterns for AWS, Azure, and Google Cloud.

### AWS

Example pattern:

**CloudTrail → CloudWatch Logs → Detection Logic → CloudWatch Alarm → Notification / Investigation**

### Microsoft Azure

Example pattern:

**Azure Resource → Diagnostic Settings → Log Analytics → Detection Logic → Alert → Investigation / Response**

### Google Cloud

Example pattern:

**Google Cloud Activity → Cloud Logging → Logs-Based Metric → Cloud Monitoring Alert → Investigation / Response**

The objective is not simply to enable logging.

A useful security monitoring architecture must connect:

**Required Security Event → Telemetry → Detection → Alert → Investigation → Response → Evidence**

---

## 6. Multi-Cloud SIEM and SOAR

The project also examines how cloud-native telemetry and findings could participate in a broader enterprise monitoring architecture.

Conceptually:

**Cloud Telemetry → Cloud-Native Collection / Detection → Enterprise Security Monitoring → Investigation → Response**

The architecture distinguishes among:

* Telemetry
* Detection
* Security findings
* Correlation
* Incidents
* Response automation

These capabilities should not be treated as interchangeable.

SOAR automation should also consider:

**Detection Confidence → Business Impact → Blast Radius → Reversibility → Required Approval**

Low-risk enrichment and ticketing may be good automation candidates, while disruptive containment actions may require additional validation or human approval.

---

## 7. Resilience and Availability

The Azure VM Scale Set example demonstrates capacity and scaling considerations.

The broader architecture distinguishes autoscaling from:

* High availability
* Backup
* Disaster recovery
* Application resilience
* Dependency resilience

For critical services, architecture decisions should follow business availability and recovery requirements rather than assuming autoscaling solves every failure scenario.

---

# Cloud Capability Comparison

The repository includes a cross-cloud capability comparison covering areas such as:

* Compute
* Storage
* Identity
* Data discovery and governance
* Data protection
* Logging and monitoring
* Configuration governance
* Networking
* Vulnerability and security posture
* Resilience

The comparison intentionally avoids treating services as exact equivalents.

The decision process is:

**Business Requirement → Security Objective → Required Capability → Cloud-Native Implementation → Evidence → Monitoring → Exceptions**

---

# ISO 27001 Context

ISO/IEC 27001 is used as a security and governance context for the project.

Cloud services can support capabilities associated with areas such as:

* Identity and access
* Information classification
* Cryptography
* Logging and monitoring
* Network security
* Configuration management
* Data protection
* Vulnerability management
* Resilience

However:

**Cloud Service ≠ ISO 27001 Control ≠ Compliance**

Actual ISO 27001 alignment depends on the organization's:

* ISMS scope
* Risk assessment
* Applicable version of the standard
* Statement of Applicability
* Policies
* Control design
* Ownership
* Operating procedures
* Evidence
* Exceptions
* Control effectiveness

This project therefore uses capability-based alignment rather than claiming that deploying a particular AWS, Azure, or Google Cloud service directly satisfies an ISO control.

---

# Repository Structure

| Folder                 | Purpose                                                                             |
| ---------------------- | ----------------------------------------------------------------------------------- |
| `Cloud Providers/`     | Cloud-specific security and configuration examples for AWS, Azure, and Google Cloud |
| `Comparisons/`         | Cross-cloud security capability comparison and architecture analysis                |
| `Docs/`                | Business requirements and supporting architecture documentation                     |
| `Security_Monitoring/` | Cloud-native logging/detection examples and multi-cloud SIEM/SOAR architecture      |
| `Automation/`          | Supporting configuration and automation examples                                    |

---

# Representative Architecture Questions

Throughout the project, I use questions such as:

* What business requirement are we trying to satisfy?
* What data or workload are we protecting?
* What could go wrong?
* Which identity is performing the action?
* What is the trust boundary?
* Which security capability is required?
* What telemetry proves the control is operating?
* What happens when the control fails?
* What should happen when a detection fires?
* Which actions can safely be automated?
* What evidence would an auditor, security team, or risk owner need?
* How does the implementation differ between clouds?

These questions are more important to the architecture than simply identifying equivalent cloud product names.

---

# Skills Demonstrated

This project demonstrates experience with:

* Multi-Cloud Security Architecture
* AWS, Microsoft Azure, and Google Cloud security capabilities
* Security requirements analysis
* Identity and access architecture
* Data discovery and classification
* Data protection
* Network security
* Logging and monitoring
* Detection architecture
* SIEM/SOAR design
* Resilience considerations
* Risk-based security decisions
* ISO 27001-aware architecture
* Cross-cloud capability analysis
* Architecture tradeoff analysis

---

# Architecture Takeaway

A multi-cloud security architecture should not begin by asking:

**“What is the Azure or Google Cloud equivalent of this AWS service?”**

For this project, I would begin with:

**“What does the business need to protect, what is the risk, and what security capability is required?”**

From there:

**Business Requirement → Risk → Security Objective → Capability → Cloud Implementation → Evidence**

That approach allows AWS, Azure, and Google Cloud to use different implementations while still supporting consistent enterprise security outcomes.
