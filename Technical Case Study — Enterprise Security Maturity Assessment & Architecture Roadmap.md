# Technical Case Study — ISO 27001 Multi-Cloud Security Architecture

## Case Study Overview

This technical case study demonstrates how business and security requirements can be translated into cloud-specific security capabilities across AWS, Microsoft Azure, and Google Cloud.

The objective is not to make the three cloud environments technically identical. Each provider implements identity, networking, data protection, monitoring, governance, and resilience differently.

The architecture objective is to establish **consistent security outcomes while allowing cloud-native implementations to differ**.

ISO/IEC 27001 provides governance context for the architecture, but this case study does not represent an ISO 27001 assessment, certification package, or complete Information Security Management System (ISMS).

The central architecture question is:

**What security outcome does the business require, and how should that outcome be implemented, monitored, and demonstrated in each cloud?**

---

## 1. Scenario

### Organization

This case study assumes an enterprise operating workloads across AWS, Microsoft Azure, and Google Cloud.

The environment contains:

* Business applications
* Sensitive and regulated data
* Internet-facing services
* Cloud-native workloads
* Administrative users
* Workload identities
* Centralized security monitoring
* Hybrid connectivity
* CI/CD and Infrastructure as Code
* Third-party integrations

The organization wants cloud teams to use native capabilities where appropriate while maintaining consistent enterprise security expectations.

### Business Drivers

The primary business drivers are:

* Support multi-cloud adoption
* Protect sensitive information
* Reduce inconsistent security implementation
* Maintain appropriate access controls
* Improve security visibility
* Strengthen audit evidence
* Support regulatory and compliance obligations
* Improve resilience
* Allow cloud teams to use native platform capabilities
* Establish repeatable architecture decisions

The security architecture therefore needs to balance enterprise consistency with cloud-specific implementation.

---

## 2. Architecture Method

For each security requirement, I use the following decision path:

**Business Requirement → Data / Workload Context → Risk → Security Objective → Required Capability → Cloud-Native Implementation → Monitoring / Evidence → Exception or Remediation**

This prevents the architecture from beginning with individual cloud products.

For example:

**Business requirement:** Sensitive customer information must be protected.

That requirement does not immediately translate to:

**Use Amazon Macie, Microsoft Purview, or Google Cloud Sensitive Data Protection.**

Those services address portions of the problem.

The architecture first needs to determine:

* What data is sensitive?
* Where does it reside?
* Who should have access?
* How is access authorized?
* How should the data be classified?
* Is masking or de-identification required?
* What encryption requirements apply?
* What telemetry is required?
* What constitutes unacceptable exposure?
* What evidence demonstrates that controls are operating?

Only then should cloud-native services be selected.

---

## 3. Multi-Cloud Design Principle

AWS, Azure, and Google Cloud provide overlapping security capabilities, but their implementations, terminology, policy models, identity structures, and operational models differ.

The architecture therefore compares platforms by **security capability**, not by assuming direct service equivalence.

The goal is:

**Consistent Security Outcome ≠ Identical Technical Implementation**

A control implemented differently in each cloud may still satisfy the same enterprise security objective.

Conversely, deploying apparently similar services in each cloud does not guarantee equivalent security outcomes.

---

## 4. Identity and Access Architecture

Identity is a foundational capability because it affects administrative access, workload access, data access, automation, and incident investigation.

### Security Objectives

The identity architecture should support:

* Strong authentication
* Least privilege
* Role-based or attribute-based authorization where appropriate
* Privileged-access control
* Workload identity
* Credential lifecycle management
* Administrative accountability
* Access governance

### AWS

Representative AWS capabilities include:

* AWS IAM
* IAM roles
* AWS IAM Identity Center
* Service roles
* Resource policies
* AWS Organizations
* CloudTrail

### Microsoft Azure

Representative Azure capabilities include:

* Microsoft Entra ID
* Azure RBAC
* Managed identities
* Conditional Access
* Privileged Identity Management
* Azure Policy
* Azure Activity Log

### Google Cloud

Representative Google Cloud capabilities include:

* Cloud IAM
* Service accounts
* IAM Conditions
* Workload Identity
* Organization Policy
* Cloud Audit Logs

### Architecture Decision

Enterprise identity requirements should define the expected security outcome.

Each cloud can then implement that requirement using its native identity model.

The organization should avoid forcing identical role structures across platforms where the underlying authorization models differ.

---

## 5. Data Discovery and Classification

Sensitive-data protection begins with knowing what data exists and where it resides.

### Architecture Flow

**Discover → Classify → Validate → Determine Exposure → Assess Risk → Remediate → Monitor**

Discovery alone is insufficient.

Finding sensitive information should trigger additional questions:

* Is the data expected to be there?
* Who can access it?
* Is the storage location approved?
* Is encryption appropriate?
* Is masking required?
* Is retention appropriate?
* Is the data externally accessible?
* Is activity being monitored?

### Representative Cloud Capabilities

**AWS**

Amazon Macie can assist with discovering and identifying sensitive information in supported AWS data stores.

**Microsoft Azure**

Microsoft Purview provides data governance, discovery, and classification capabilities across supported environments.

**Google Cloud**

Google Cloud Sensitive Data Protection provides inspection, classification, and de-identification capabilities.

### Architecture Decision

These services should be treated as components within a broader data-security architecture rather than as complete Data Loss Prevention solutions by themselves.

---

## 6. Data Protection Architecture

Different data-protection mechanisms address different threats.

The architecture distinguishes among:

* Encryption
* Key management
* Masking
* Tokenization
* De-identification
* Authorization
* Classification
* Monitoring
* Retention

### Encryption

Encryption protects data against particular storage, media, or access threats depending on how keys and access are controlled.

Representative capabilities include:

* AWS KMS
* Azure Key Vault
* Google Cloud KMS

### Masking and De-Identification

Masking or de-identification limits exposure of sensitive values to users or applications that do not require the original information.

This is different from encryption.

An application may be authorized to decrypt a database while still requiring masked values for certain users.

### Architecture Decision

The protection mechanism should follow the threat and business requirement rather than assuming encryption alone satisfies every data-security requirement.

---

## 7. Network Security Architecture

Cloud network controls should be derived from required communication paths.

The architecture begins with:

**Who needs connectivity → From where → To what workload → Using what protocol → For what purpose**

### Security Considerations

The design considers:

* Trust boundaries
* Segmentation
* Administrative access
* Ingress
* Egress
* Private connectivity
* Firewall policy
* Workload targeting
* DNS
* Network telemetry

### Cloud Implementations

AWS, Azure, and Google Cloud each provide cloud-native network security controls.

The exact mechanism differs, but the enterprise requirement remains consistent:

**Only explicitly required communication should be permitted across defined trust boundaries.**

### Architecture Decision

Network policy should represent application and business communication requirements rather than being constructed solely around cloud network objects.

---

## 8. Logging, Monitoring, and Detection

Logging does not provide security value merely because logs exist.

The architecture needs to connect:

**Required Security Event → Telemetry → Detection → Alert → Investigation → Response → Evidence**

### AWS Pattern

**CloudTrail → CloudWatch Logs → Detection Logic → CloudWatch Alarm → Notification / Investigation**

### Azure Pattern

**Azure Resource → Diagnostic Settings → Log Analytics → Detection Logic → Alert → Investigation / Response**

### Google Cloud Pattern

**Google Cloud Activity → Cloud Logging → Logs-Based Metric → Cloud Monitoring Alert → Investigation / Response**

### Architecture Decision

Each cloud can retain native telemetry and detection capabilities while forwarding appropriate security information into enterprise monitoring.

The architecture should define:

* Required events
* Log sources
* Retention
* Detection requirements
* Alert ownership
* Escalation
* Investigation procedures
* Evidence requirements

---

## 9. Multi-Cloud SIEM and SOAR Architecture

Enterprise security monitoring may aggregate security telemetry and findings from multiple cloud environments.

Conceptually:

**Cloud Telemetry → Cloud-Native Collection / Detection → Enterprise Security Monitoring → Investigation → Response**

The architecture distinguishes among:

* Raw telemetry
* Detection logic
* Security findings
* Correlation
* Alerts
* Incidents
* Automated response

These are not interchangeable.

### SOAR Decision Model

Automated response should consider:

**Detection Confidence → Business Impact → Blast Radius → Reversibility → Required Approval**

Low-risk actions such as enrichment, evidence collection, notification, or ticket creation may be appropriate for automation.

Actions that could interrupt business services may require additional validation or human approval.

### Architecture Decision

Automation should not be selected merely because an action can technically be automated.

The response mechanism should reflect the risk of both the security event and the automated action itself.

---

## 10. Security Governance and Guardrails

Multi-cloud architecture requires governance that establishes consistent security expectations without eliminating legitimate platform differences.

Potential governance mechanisms include:

* Architecture standards
* Cloud security baselines
* Policy-as-Code
* Infrastructure as Code validation
* Cloud-native policy controls
* Architecture review
* Security exceptions
* Risk acceptance
* Continuous monitoring

### Preventive Controls

Prevent configurations that should not be permitted.

Examples may include:

* Prohibited public exposure
* Disallowed regions
* Missing required encryption
* Unauthorized administrative configurations

### Detective Controls

Identify conditions that cannot reasonably be prevented or that may emerge through configuration drift.

### Corrective Controls

Remediate well-understood violations when automation is sufficiently safe.

### Architecture Decision

Not every violation should trigger automatic remediation.

Corrective automation should consider:

* Confidence
* Business impact
* Blast radius
* Reversibility
* Ownership
* Required approval

---

## 11. Exception Management

Enterprise standards cannot anticipate every legitimate workload requirement.

The architecture therefore requires a controlled exception process.

An exception should identify:

* Requirement being excepted
* Business justification
* Affected workload
* Risk
* Compensating controls
* Risk owner
* Approval
* Expiration date
* Review requirements

Exceptions should not become permanent undocumented bypasses.

### Architecture Decision

A mature control environment needs both strong guardrails and a governed mechanism for handling legitimate exceptions.

---

## 12. Resilience and Availability

Security architecture also needs to consider availability and recovery.

The project distinguishes among:

* Autoscaling
* High availability
* Backup
* Disaster recovery
* Application resilience
* Dependency resilience

These capabilities solve different problems.

### Architecture Decision

Availability architecture should follow business requirements such as:

* Criticality
* Recovery Time Objective
* Recovery Point Objective
* Geographic requirements
* Dependency tolerance
* Data durability
* Cost

Autoscaling should not be treated as equivalent to high availability or disaster recovery.

---

## 13. ISO 27001 Governance Context

ISO/IEC 27001 provides governance context for this architecture.

Cloud security capabilities can support areas associated with:

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

An organization's ISO 27001 alignment depends on factors including:

* ISMS scope
* Risk assessment
* Applicable standard version
* Statement of Applicability
* Policies
* Control ownership
* Procedures
* Evidence
* Exceptions
* Control operation
* Control effectiveness

### Architecture Decision

This project therefore uses **capability-based alignment** rather than claiming that deployment of a particular AWS, Azure, or Google Cloud service directly satisfies an ISO control.

---

## 14. Architecture Review Questions

For a multi-cloud workload, I would ask:

* What business requirement does this architecture support?
* What data is processed?
* How sensitive is that data?
* Which identities access the workload?
* Which workload identities exist?
* What are the trust boundaries?
* What communication paths are required?
* What security capabilities are required?
* Which controls are preventive?
* Which controls are detective?
* Which controls are corrective?
* What telemetry demonstrates control operation?
* What happens if a control fails?
* What happens if logging fails?
* What happens if identity services are unavailable?
* Which response actions can safely be automated?
* What requires human approval?
* What evidence is retained?
* Are exceptions required?
* Who owns the residual risk?

These questions provide a consistent architecture-review approach even when the technical implementations differ across AWS, Azure, and Google Cloud.

---

## 15. Failure Paths

Security architecture needs to account for control failure.

### Identity Failure

If centralized identity or federation becomes unavailable, emergency administrative access may be required.

Emergency access should be:

* Limited
* Strongly protected
* Monitored
* Tested
* Audited
* Reviewed after use

### Logging Failure

If centralized log ingestion fails, critical telemetry should not disappear without detection.

The architecture should consider:

* Ingestion health monitoring
* Local or intermediate retention
* Alerting on telemetry loss
* Recovery of missing events

### Policy Failure

If a preventive policy incorrectly blocks a legitimate deployment, teams need a controlled exception mechanism.

The alternative should not be bypassing enterprise controls.

### Automated Response Failure

If an automated response could interrupt legitimate business activity, the architecture should define safeguards, approvals, rollback, and escalation before the event occurs.

---

## 16. Validation and Evidence

A control should not be considered effective merely because its technology has been deployed.

Validation should demonstrate that the intended security outcome is being achieved.

Representative evidence could include:

### Identity

* MFA coverage
* Privileged-access activity
* Access-review results
* Workload-identity usage
* Authentication telemetry

### Data Protection

* Classification results
* Encryption configuration
* Key usage
* Access-policy validation
* Sensitive-data findings
* Remediation evidence

### Network Security

* Approved communication paths
* Firewall-policy validation
* Segmentation testing
* Flow telemetry
* Exception records

### Monitoring

* Required log-source coverage
* Detection results
* Alert testing
* Incident evidence
* Telemetry-health monitoring

### Governance

* Architecture decisions
* Policy results
* Exceptions
* Risk acceptance
* Remediation tracking

Evidence connects technical implementation to governance and assurance.

---

## 17. Key Architecture Tradeoffs

### Standardization vs. Cloud-Native Capability

Excessive standardization can prevent teams from taking advantage of cloud-native security capabilities.

Insufficient standardization can create inconsistent security outcomes.

The architecture therefore standardizes **requirements and outcomes** while allowing implementation differences where justified.

### Prevention vs. Operational Flexibility

Preventive controls provide strong enforcement but can disrupt legitimate workloads if requirements are poorly defined.

Detective controls provide greater flexibility but allow insecure conditions to exist until detected and remediated.

The appropriate balance depends on risk.

### Automation vs. Human Oversight

Automation improves consistency and response speed.

Human approval may still be required where actions have substantial business impact or uncertain consequences.

### Centralization vs. Cloud-Native Monitoring

Centralized monitoring improves enterprise visibility and correlation.

Cloud-native monitoring retains platform context and may provide faster access to provider-specific events.

The architecture can use both rather than treating them as mutually exclusive.

---

## 18. Architecture Outcome

The resulting architecture does not attempt to make AWS, Azure, and Google Cloud identical.

Instead, it establishes a repeatable decision model:

**Business Requirement → Risk → Security Objective → Capability → Cloud Implementation → Monitoring → Evidence → Exception / Remediation**

That model allows different cloud-native technologies to support consistent enterprise security expectations.

The key architectural principle is:

**Standardize the security outcome. Adapt the implementation to the platform.**

ISO 27001 provides governance context, but compliance remains an organizational responsibility involving risk management, policies, ownership, evidence, control operation, and continual improvement.

The value of the architecture is therefore not a list of equivalent cloud products.

It is a method for making defensible security decisions across different cloud platforms while maintaining traceability to business requirements, risk, and governance.
