# Technical Case Study — Enterprise Security Maturity Assessment & Architecture Roadmap

## Case Study Overview

This fictional case study demonstrates how I would apply the Enterprise Security Maturity Framework to assess an organization's current security capabilities, define appropriate target states, identify gaps, and turn those findings into an architecture roadmap.

The scenario uses a regulated financial-services organization that is expanding its cloud footprint while continuing to operate existing enterprise applications and infrastructure.

The purpose of the assessment is not to assign maturity scores for their own sake. The purpose is to use evidence, business context, technical dependencies, and risk to determine where architecture improvements should be made first.

---

## 1. Scenario

### Organization

**Example Financial Services Corp**

### Environment

For this scenario, I assumed the organization operates:

- Existing enterprise applications and infrastructure
- A growing cloud footprint
- Internet-facing customer services
- Sensitive customer and financial data
- Central identity services
- CI/CD pipelines supporting application delivery
- A centralized SIEM with incomplete telemetry coverage
- SaaS and third-party service providers

The organization is subject to regulatory and audit requirements and has availability expectations for critical financial services.

### Business Drivers

The primary drivers are:

- Expand cloud adoption
- Protect regulated and sensitive data
- Improve software delivery
- Improve detection and incident response
- Reduce manual security processes
- Improve resilience
- Provide stronger audit evidence
- Establish security controls that can scale with transformation

---

## 2. Assessment Method

I would assess the environment across eight security domains:

1. Governance & Risk
2. Identity & Access Management
3. Data Protection
4. Monitoring & Logging
5. Cloud Platform
6. Network Security
7. DevSecOps & CI/CD
8. Third-Party / Vendor Risk

Each capability uses the framework's five-level maturity scale:

| Level | Maturity |
|---:|---|
| 1 | Ad Hoc |
| 2 | Repeatable |
| 3 | Defined |
| 4 | Measured |
| 5 | Optimized |

For this scenario, I would not determine maturity from interviews alone.

I would compare stakeholder input with technical, operational, and governance evidence.

---

## 3. Evidence I Would Examine

### Identity & Access Management

I would examine:

- Identity architecture
- Identity provider configuration
- MFA coverage
- Conditional access policies
- Privileged-access processes
- Joiner, mover, and leaver workflows
- Access-review results
- Role and group structures
- Service accounts
- Workload identities
- Authentication logs
- Privileged-access logs
- Exception records

I would be looking for both control design and evidence that those controls operate consistently.

For example, an MFA policy does not demonstrate mature MFA coverage if important applications, privileged accounts, or administrative paths remain outside enforcement.

---

### Cloud Platform

I would examine:

- Cloud account or subscription hierarchy
- Landing zone architecture
- Network topology
- IAM integration
- Infrastructure as Code repositories
- Provisioning workflows
- Policy and guardrail configuration
- Logging baselines
- Security monitoring integration
- Backup and recovery configuration
- Cloud compliance reporting
- Workload onboarding procedures
- Architecture exceptions

I would specifically look for differences between documented standards and how workloads are actually deployed.

---

### Data Protection

I would examine:

- Data inventories
- Classification standards
- Data-flow diagrams
- Encryption coverage
- Key-management configurations
- Secrets-management practices
- Access policies
- Backup configuration
- Recovery-test results
- Retention requirements
- DLP coverage
- Tokenization or masking where applicable
- Audit findings involving sensitive data

The objective would be to understand not only whether protection technologies exist but whether they consistently protect the data that matters.

---

### Monitoring & Logging

I would examine:

- Logging architecture
- SIEM integrations
- Cloud audit logging
- Identity telemetry
- Application logging
- Network telemetry
- Critical asset coverage
- Detection rules
- Alert history
- Alert tuning
- Incident response playbooks
- Escalation workflows
- Retention
- Searchability
- Telemetry-health reporting

One question I would want answered is:

**If a material security event occurred in a critical service, would the organization have the telemetry required to detect it, investigate it, and reconstruct what happened?**

---

### Network Security

I would examine:

- Network architecture diagrams
- Trust boundaries
- VPC/VNet architecture
- Subnet design
- Firewall rules
- Security groups
- ACLs
- Ingress and egress controls
- Remote-access architecture
- Hybrid connectivity
- DNS controls
- WAF and IDS/IPS deployment
- Network telemetry
- Segmentation validation
- Rule-review and exception processes

I would pay particular attention to whether segmentation exists only architecturally or whether communication between segments is actually restricted.

---

### DevSecOps & CI/CD

I would examine:

- Pipeline configurations
- Source-control protections
- Pull-request requirements
- SAST results
- SCA results
- Secrets scanning
- IaC scanning
- DAST where applicable
- Container scanning where applicable
- Artifact repositories
- SBOM generation
- Pipeline identities
- Secrets management
- Security gates
- Exception workflows
- Deployment approvals
- Rollback procedures
- Pipeline audit logs

A security tool being installed would not by itself raise the maturity score.

I would look at coverage, enforcement, bypass paths, exception handling, ownership, and whether findings affect deployment decisions.

---

### Third-Party / Vendor Risk

I would examine:

- Vendor inventory
- Vendor classification
- Due diligence
- Security assessments
- SOC and other assurance reports
- Contractual security requirements
- Data-processing agreements
- Vendor access
- External integrations
- Shared-responsibility documentation
- Reassessment schedules
- Vendor findings
- Offboarding evidence
- Critical-service dependencies

For assurance reports, I would evaluate their relevance and scope rather than treating their existence as sufficient evidence.

---

### Governance & Risk

I would examine:

- Security policies
- Architecture standards
- Risk registers
- Risk-acceptance records
- Exception processes
- Governance committees
- Control ownership
- Audit findings
- Security metrics
- Architecture-review processes
- Investment and remediation roadmaps

This domain helps determine whether the technical controls are supported by repeatable decision-making and accountability.

---

## 4. Current and Target State

Based on the fictional evidence collected, I assigned the following current and target maturity levels:

| Domain | Current | Target | Gap | Maturity Attainment |
|---|---:|---:|---:|---:|
| IAM | 2 | 4 | 2 | 50% |
| Cloud Platform | 1 | 4 | 3 | 25% |
| Data Protection | 3 | 4 | 1 | 75% |
| DevSecOps & CI/CD | 3 | 4 | 1 | 75% |
| Monitoring & Logging | 2 | 4 | 2 | 50% |
| Network Security | 3 | 4 | 1 | 75% |
| Third-Party / Vendor Risk | 4 | 4 | 0 | 100% |
| Governance & Risk | 3 | 4 | 1 | 75% |

### Overall Target Attainment

**65.6%, rounded to 66%**

This number represents progress toward the defined target maturity.

It does not represent a percentage of security effectiveness or a percentage of risk eliminated.

---

## 5. Why the Target Was Level 4

For this scenario, I selected Level 4 as the target across the assessed domains for simplicity and consistency in demonstrating the framework.

In a real assessment, I would not automatically assign the same target maturity to every domain.

Target maturity would need to reflect:

- Business criticality
- Data sensitivity
- Regulatory requirements
- Threat exposure
- Architecture
- Operational capacity
- Availability requirements
- Risk tolerance
- Cost
- Transformation objectives

Some capabilities could reasonably require a higher target while others could justify a lower one.

The target should represent the capability required by the organization, not the highest maturity score available.

---

## 6. Representative Scoring Rationale

### IAM — Current 2 / Target 4

I scored IAM at Level 2 because foundational identity controls exist, but enforcement and lifecycle automation remain inconsistent.

Representative evidence:

- MFA exists but is not universally enforced.
- Access reviews remain heavily manual.
- Joiner/mover/leaver automation is incomplete.
- Privileged-access controls vary by environment.
- Workload and service identities lack consistent governance.

The organization has repeatable identity practices, but I would not consider them sufficiently standardized, measured, and enforced for Level 3 or 4.

---

### Cloud Platform — Current 1 / Target 4

Cloud Platform received the lowest current score.

Representative evidence:

- No consistent enterprise landing zone.
- Account or subscription architecture varies.
- Provisioning includes significant manual configuration.
- Guardrails are inconsistent.
- Logging requirements are not automatically enforced.
- Workload onboarding varies by team.

Although individual cloud controls exist, the enterprise platform itself lacks a sufficiently repeatable architecture.

---

### Monitoring & Logging — Current 2 / Target 4

Representative evidence:

- A SIEM exists.
- Some critical systems send centralized telemetry.
- Cloud and identity logging coverage is incomplete.
- Alert quality varies.
- Detection and response workflows are only partially integrated.
- Coverage is not consistently measured.

The presence of a SIEM therefore does not justify a high maturity score.

---

### DevSecOps & CI/CD — Current 3 / Target 4

Representative evidence:

- CI/CD pipelines are established.
- SAST and dependency scanning exist.
- IaC is used for portions of the environment.
- Security scanning is not consistently enforced.
- Gate thresholds vary.
- Exception handling is inconsistent.
- Pipeline identity requires improvement.
- Supply-chain evidence is incomplete.

This is a defined capability that requires stronger measurement and enforcement rather than a complete redesign.

---

## 7. Maturity Gap Versus Risk Priority

The largest maturity gap is Cloud Platform:

**Target 4 - Current 1 = Gap 3**

That does not automatically make Cloud Platform Priority 1.

For prioritization, I would consider maturity alongside:

- Risk severity
- Business criticality
- Regulatory exposure
- Scope
- Dependencies
- Compensating controls
- Cost and effort
- Expected risk reduction

Conceptually:

**Priority = Maturity Gap × Risk × Weighting Factors**

I would not treat that formula as a universal mathematical risk model. The actual weighting and scoring method would need to be defined by the organization.

Its purpose in this framework is to make one distinction clear:

**Maturity measures capability. Risk prioritization determines what should be addressed first.**

---

## 8. Risk and Dependency Analysis

| Finding | Maturity Gap | Business / Security Risk | Dependency Value | Priority |
|---|---:|---|---|---:|
| IAM lifecycle and privileged access | 2 | Critical | Foundational across cloud, data, applications | 1 |
| Monitoring and telemetry coverage | 2 | High | Required for detection, IR, and control evidence | 2 |
| Cloud platform standardization | 3 | High | Required for scalable cloud adoption | 3 |
| CI/CD security enforcement | 1 | Medium / High | Builds on existing delivery capability | 4 |

This is where architectural judgment changes the roadmap.

---

## 9. Architecture Decision 1 — Identity Before Broad Cloud Expansion

The first architecture priority is IAM.

The reason is not that IAM has the lowest maturity score.

It does not.

The reason is that identity is a dependency for:

- Administrative access
- Cloud access
- Privileged operations
- Application access
- Data access
- Remote access
- Workload identity
- Zero Trust

### Architecture Direction

I would prioritize:

- Broad MFA enforcement
- Privileged-access controls
- Joiner/mover/leaver automation
- Recurring access certification
- Role and entitlement cleanup
- Service-account governance
- Workload identity
- Short-lived credentials where supported
- Centralized identity telemetry

### Expected Evidence

Improvement could be demonstrated through:

- MFA coverage
- Reduced unmanaged privileged accounts
- Access-review completion
- Automated deprovisioning
- Reduced standing privilege
- Workload-identity adoption
- Authentication and authorization telemetry

---

## 10. Architecture Decision 2 — Establish Monitoring as a Foundational Control

Monitoring becomes the second priority because the organization needs evidence that security controls are operating and visibility when they fail.

### Architecture Direction

I would establish minimum telemetry requirements for critical services.

These would include appropriate:

- Identity events
- Cloud control-plane events
- Administrative activity
- Application security events
- Network-security telemetry
- Configuration changes

Critical telemetry would feed centralized monitoring with defined:

- Retention
- Ownership
- Alerting
- Escalation
- Investigation procedures

### Expected Evidence

I would expect to see:

- Increased critical-asset logging coverage
- Reduced telemetry gaps
- Defined detection rules
- Improved alert quality
- Incident playbook integration
- Measurable detection and response performance

---

## 11. Architecture Decision 3 — Build the Cloud Foundation with Controls Included

Once foundational IAM and monitoring requirements are established, I would address the Cloud Platform maturity gap through a standardized landing zone approach.

I would not design the landing zone first and attempt to bolt security onto it later.

### Architecture Direction

The cloud foundation would establish:

- Account or subscription structure
- Environment separation
- Network architecture
- Identity integration
- Privileged-access patterns
- Logging baselines
- Security policies and guardrails
- IaC provisioning
- Backup and recovery expectations
- Workload onboarding
- Exception handling

### Preventive, Detective, and Corrective Controls

Where appropriate, I would use:

**Preventive controls**

to stop prohibited configurations before or during deployment.

**Detective controls**

to identify configuration drift or violations that cannot reasonably be prevented.

**Corrective controls**

to remediate well-understood conditions where automation is safe and appropriate.

Not every violation should trigger automatic remediation. The response should reflect potential business impact and confidence in the corrective action.

---

## 12. Architecture Decision 4 — Standardize CI/CD Security Enforcement

The organization already has functioning CI/CD capability.

The architecture problem is consistency.

### Architecture Direction

I would establish a control progression that could include:

1. Source-control protections
2. Secrets scanning
3. SAST
4. SCA
5. IaC scanning
6. Container scanning where applicable
7. SBOM generation where required
8. DAST where appropriate
9. Artifact integrity controls
10. Security gates
11. Exception management
12. Controlled deployment

The exact controls would depend on the application and delivery model.

### Gate Design

A finding should not automatically block a release merely because a scanner produced it.

Gate criteria should consider:

- Severity
- Exploitability
- Application exposure
- Asset criticality
- Data sensitivity
- Existing mitigations
- Organizational policy

Exceptions should be:

- Documented
- Risk accepted by an appropriate owner
- Time-bound
- Traceable
- Reassessed

---

## 13. Cross-Domain Dependencies

One reason I would avoid treating maturity domains independently is that improvements frequently depend on each other.

### IAM → Cloud Platform

Cloud guardrails depend on reliable identity and privileged-access patterns.

### IAM → Network Security

Zero Trust access decisions increasingly require identity and context rather than relying only on network location.

### Monitoring → IAM

Authentication and privileged-access telemetry are needed to detect identity misuse.

### Cloud Platform → Monitoring

A standardized landing zone can enforce logging requirements during workload onboarding.

### Cloud Platform → Network Security

Reusable cloud network patterns can enforce segmentation and connectivity requirements.

### DevSecOps → Cloud Platform

IaC and pipeline controls can prevent noncompliant infrastructure from reaching production.

### Data Protection → IAM

Sensitive-data access depends on authorization and least privilege.

### Data Protection → Monitoring

Sensitive-data activity requires appropriate telemetry.

### Governance → All Domains

Exceptions, risk acceptance, ownership, standards, and target maturity require governance.

The roadmap therefore needs to account for these relationships rather than funding eight isolated maturity initiatives.

---

## 14. Roadmap

### Phase 1 — Foundation and Visibility

**0–3 Months**

IAM:

- Validate MFA coverage
- Review privileged access
- Identify unmanaged service identities
- Identify high-risk lifecycle gaps

Monitoring:

- Inventory critical telemetry
- Identify logging blind spots
- Establish minimum logging requirements
- Onboard priority sources

Cloud:

- Define landing zone requirements
- Define account/subscription hierarchy
- Identify baseline identity and logging requirements

DevSecOps:

- Inventory pipeline controls
- Identify inconsistent gates
- Review pipeline identities and secrets

---

### Phase 2 — Standardization and Enforcement

**3–12 Months**

IAM:

- Increase lifecycle automation
- Establish recurring access certification
- Strengthen privileged-access management
- Improve workload identity

Monitoring:

- Standardize cloud logging
- Expand detection coverage
- Improve alert tuning
- Integrate incident playbooks

Cloud:

- Deploy standardized landing zone patterns
- Increase IaC adoption
- Establish network baselines
- Implement policy guardrails
- Formalize workload onboarding

DevSecOps:

- Standardize security scanning
- Establish gate criteria
- Formalize exceptions
- Improve artifact controls
- Strengthen pipeline identity

---

### Phase 3 — Measurement and Optimization

**12+ Months**

Potential initiatives include:

- Risk-based Zero Trust enforcement
- Continuous compliance
- Automated remediation for appropriate conditions
- Improved detection engineering
- Software supply-chain assurance
- Cross-domain security metrics
- Continuous control validation
- Recurring maturity reassessment

The roadmap would be adjusted as evidence and business priorities change.

---

## 15. Validation

I would not consider an initiative complete because a technology was deployed.

The target maturity is Level 4, which requires evidence that controls are operating consistently and are being measured.

Examples of validation include:

### IAM

- MFA coverage
- Privileged-access review results
- Access-certification completion
- Deprovisioning performance
- Workload-identity adoption

### Monitoring

- Percentage of critical assets meeting telemetry requirements
- Detection coverage
- Alert quality
- Incident-response metrics
- Telemetry availability

### Cloud Platform

- Percentage of workloads using approved landing-zone patterns
- IaC adoption
- Policy compliance
- Exception volume
- Logging-baseline compliance

### DevSecOps

- Percentage of pipelines using required security controls
- Gate failure trends
- Exception age
- Finding remediation
- Pipeline identity compliance
- Artifact and SBOM coverage where required

The measurements selected should demonstrate whether the intended control outcome is actually being achieved.

---

## 16. Failure Paths and Architecture Considerations

A maturity roadmap also needs to account for how controls can fail.

Examples include:

### Identity

If the identity provider or federation path becomes unavailable, critical administrative access requires a controlled recovery mechanism.

Any emergency access should be:

- Limited
- Protected
- Monitored
- Tested
- Reviewed after use

### Logging

If centralized ingestion fails, critical telemetry should not silently disappear.

The architecture should consider:

- Detection of ingestion failure
- Local or intermediate retention where appropriate
- Recovery of missing events
- Alerting on telemetry loss

### Cloud Guardrails

If a policy blocks a legitimate business deployment, there needs to be a controlled exception path rather than teams bypassing the platform.

### CI/CD Security Gates

If a scanner or dependency service is unavailable, the organization needs a defined fail-open or fail-closed decision based on application risk and the control involved.

These decisions should be made before an outage or urgent release forces teams to improvise.

---

## 17. Reassessment

The maturity assessment is a point-in-time architecture view.

I would reassess after significant changes such as:

- Cloud expansion
- Major platform modernization
- Identity transformation
- Regulatory change
- Significant incidents
- Acquisitions
- Major third-party changes
- Completion of roadmap initiatives

Scores may increase, decrease, or remain unchanged depending on the evidence.

Target maturity may also change as the business changes.

---

## 18. Final Architecture Perspective

The most important result of this assessment is not that the fictional organization achieved approximately 66% of its target maturity.

The important result is the decision path:

**Business Drivers → Evidence → Current State → Target State → Gap → Risk → Dependencies → Architecture Priorities → Roadmap → Validation**

For this scenario, that process led to:

**IAM → Monitoring & Logging → Cloud Platform → DevSecOps & CI/CD**

Cloud Platform had the largest maturity gap, but IAM was the more important starting point because identity was a foundational dependency across the environment.

Monitoring followed because architecture decisions and security controls need visibility and evidence.

Cloud Platform could then be standardized with identity and telemetry requirements built into the foundation.

DevSecOps improvements could build on an existing Level 3 delivery capability rather than competing with the more foundational work.

That distinction is the purpose of the maturity framework.

**Maturity tells me where capability stands. Risk and architecture judgment tell me what I would do about it.**