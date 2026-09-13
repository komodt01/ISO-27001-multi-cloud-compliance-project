# Multi-Cloud SIEM and SOAR Architecture

## Purpose

This document describes an architecture approach for security monitoring, detection, investigation, and response across AWS, Microsoft Azure, and Google Cloud.

The objective is not to force each cloud into an identical SIEM/SOAR implementation.

For this scenario, I would use cloud-native telemetry and security capabilities where they provide value, while determining which events and findings need to be centralized for enterprise correlation, investigation, compliance evidence, and incident response.

This document represents an **architecture pattern**, not a claim that a production multi-cloud SIEM/SOAR platform was deployed.

---

## 1. Architecture Approach

The basic monitoring flow is:

**Cloud Telemetry → Cloud-Native Collection / Detection → Enterprise Security Monitoring → Investigation → Response**

Sources may include:

### AWS

* AWS CloudTrail
* Amazon CloudWatch
* Amazon GuardDuty
* AWS Security Hub
* AWS Config
* Application and workload logs

### Microsoft Azure

* Azure Activity Log
* Azure Monitor
* Log Analytics
* Microsoft Defender for Cloud
* Microsoft Sentinel where used
* Application and workload logs

### Google Cloud

* Cloud Audit Logs
* Cloud Logging
* Cloud Monitoring
* Security Command Center
* Application and workload logs

The enterprise architecture then determines which telemetry remains within the cloud platform and which information is forwarded to a centralized security-monitoring platform.

---

## 2. SIEM Architecture

A SIEM should provide more than centralized log storage.

For this scenario, I would expect the enterprise monitoring capability to support:

* Log ingestion
* Parsing
* Normalization
* Correlation
* Detection
* Investigation
* Alerting
* Retention
* Search
* Reporting
* Evidence preservation

Potential enterprise SIEM platforms could include Microsoft Sentinel, Splunk, Elastic Security, Google Security Operations, or another organization-approved platform.

The product choice should follow the organization's requirements rather than assuming that every cloud requires its own independent SIEM.

---

## 3. Cloud-Native Security Services

Cloud-native security services still have an important role.

For example, AWS Security Hub can aggregate and normalize security findings from AWS security services and supported integrations.

That does **not** make Security Hub a SIEM.

Similarly, services such as GuardDuty, Defender for Cloud, and Security Command Center provide security capabilities that can generate findings or signals that may ultimately feed a broader monitoring and incident-response process.

For this architecture, I would distinguish between:

**Telemetry → Detection → Finding → Correlation → Incident → Response**

Those are related capabilities, but they are not the same thing.

---

## 4. Telemetry Requirements

Before forwarding logs, I would determine what security questions the organization needs to answer.

Relevant telemetry could include:

* Authentication activity
* Privileged access
* IAM changes
* Administrative API activity
* Network activity
* Security findings
* Data-access activity
* Configuration changes
* Application events
* Workload activity
* Key-management events

Collecting every possible event indefinitely is not automatically a stronger security architecture.

---

## 5. Cross-Cloud Normalization

AWS, Azure, and Google Cloud represent events differently.

For cross-cloud detection, relevant events may need normalization into common concepts such as:

* Timestamp
* Identity
* Source
* Destination
* Action
* Resource
* Result
* Severity
* Environment
* Cloud provider
* Account / subscription / project
* Region
* Correlation identifier

Normalization allows analysts and detection logic to reason about security activity consistently without pretending the underlying cloud platforms are identical.

---

## 6. Retention and Cost

Security telemetry can become expensive at enterprise scale.

I would classify telemetry based on factors such as:

* Detection value
* Investigation value
* Compliance requirement
* Business criticality
* Required retention
* Query frequency
* Storage cost

This could lead to different retention tiers rather than sending every event into the most expensive analytics tier.

The objective is:

**Collect what is required → Retain it appropriately → Make important events searchable → Preserve required evidence**

---

## 7. Detection and Alerting

Not every event should become an alert.

Detection logic should identify behavior that warrants investigation.

Examples could include:

* Unexpected privileged access
* High-risk authentication activity
* Unauthorized IAM changes
* Security-control modification
* Suspicious network activity
* Sensitive-data exposure
* Logging being disabled
* Unexpected key-management activity
* High-risk configuration drift

Alert thresholds should balance detection coverage against analyst workload and false positives.

---

## 8. SOAR and Response Automation

SOAR can automate repeatable parts of incident investigation and response.

Potential use cases include:

* Enriching an alert with identity or asset information
* Gathering related security events
* Creating an incident ticket
* Routing an incident to the appropriate team
* Collecting evidence
* Executing approved containment actions
* Tracking remediation

I would not automatically apply destructive or high-impact remediation to every alert.

---

## 9. Automation Decision Model

Before automating a response, I would evaluate:

**Detection Confidence → Business Impact → Blast Radius → Reversibility → Required Approval**

For example:

### Lower-Risk Automation

An alert could automatically:

* Gather supporting logs
* Query asset information
* Add threat context
* Open a ticket
* Notify the appropriate team

### Higher-Risk Automation

Actions such as:

* Disabling an account
* Blocking network connectivity
* Quarantining a workload
* Revoking credentials
* Changing firewall policy

may require additional validation or human approval depending on the environment.

---

## 10. Failure Paths

Automation also needs a defined failure model.

### SIEM Ingestion Failure

If telemetry stops arriving:

* Detect the ingestion failure.
* Alert the monitoring team.
* Preserve logs at the source where possible.
* Restore the collection path.
* Determine whether events were lost.
* Recover missing telemetry where supported.

A monitoring system that silently stops receiving logs creates its own security risk.

### SOAR Failure

If an automated response fails:

* Preserve the incident state.
* Record the failed action.
* Escalate to an analyst.
* Avoid repeatedly executing potentially disruptive actions.
* Provide a manual response path.

### Incorrect Detection

If a detection is a false positive, automated containment could interrupt legitimate business activity.

That is why response automation should be proportional to detection confidence and business impact.

---

## 11. Identity for Monitoring and Automation

Log collectors and response automation require identities of their own.

I would apply the same identity principles used elsewhere in the architecture:

* Least privilege
* Defined ownership
* No shared credentials
* Short-lived credentials where possible
* Workload identity or managed identity where supported
* Credential rotation where persistent credentials are unavoidable
* Administrative logging
* Separation of duties

Long-lived service-account keys or embedded client secrets should not be the default architecture.

---

## 12. Threat Intelligence

Threat intelligence can enrich security detections and investigations.

Conceptually:

**Threat Intelligence → Detection / Enrichment → Investigation**

It is an input to the monitoring process rather than simply an output from SOAR.

Threat intelligence should also be evaluated for:

* Source reliability
* Relevance
* Freshness
* Confidence
* False positives

An indicator match alone may not justify an automated containment action.

---

## 13. Compliance and Evidence

Security monitoring can provide evidence supporting an organization's compliance program.

Examples include evidence of:

* Administrative activity
* Authentication
* Security-control changes
* Detection
* Incident handling
* Access
* Configuration changes

The SIEM does not determine ISO 27001 compliance.

The organization still needs to define its ISMS scope, risks, applicable controls, Statement of Applicability, control ownership, procedures, and evidence of effectiveness.

---

## 14. Architecture Decision Flow

For this scenario, I would use:

**Business / Security Requirement → Required Telemetry → Collection → Detection → Correlation → Investigation → Response Decision → Automation or Human Action → Evidence**

This keeps the architecture focused on the security outcome rather than starting with a SIEM or SOAR product.

---

## Architecture Takeaway

The central multi-cloud monitoring question is not:

**“How do I send every cloud log into one tool?”**

I would instead ask:

**“Which security events do we need to see, what do we need to detect, where should correlation occur, and what response is appropriate when the detection fires?”**

The technology choices follow those requirements.
