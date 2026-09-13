# Azure Data Protection — Masking and Classification

## Purpose

This module demonstrates two data-protection concepts within the Microsoft Azure ecosystem:

* Dynamic Data Masking for Azure SQL
* Sensitivity classification and labeling through Microsoft Purview

These capabilities address different parts of the data-protection lifecycle.

**Dynamic Data Masking** limits unnecessary exposure of sensitive database values to users who do not require the complete data.

**Sensitivity labels and classifications** identify the business sensitivity of information so that appropriate handling and protection requirements can be applied.

Neither capability should be treated as a complete Data Loss Prevention solution or as proof of ISO 27001 compliance.

---

## Architecture Context

For this scenario, I would separate the capabilities into two control paths.

### Data Masking

**User / Application → Authorization → Azure SQL → Dynamic Data Masking → Appropriate Data View**

### Classification

**Enterprise Data → Discovery / Classification → Sensitivity Label → Protection and Governance Requirements**

These controls should ultimately work together with identity, encryption, monitoring, retention, and data-governance processes.

---

## 1. Azure SQL Dynamic Data Masking

Azure SQL Dynamic Data Masking can limit the exposure of sensitive values returned by database queries.

For example, a database may contain a complete sensitive value while presenting only a masked representation to users who do not require access to the original value.

Conceptually:

```text
Stored Value:
123-45-6789

Masked View:
XXX-XX-6789
```

The underlying data remains unchanged.

### Architecture Considerations

Before implementing Dynamic Data Masking, I would determine:

* Which columns contain sensitive data?
* Which users or applications require complete values?
* Which users should receive masked values?
* How is authorization enforced?
* Can privileged users bypass masking?
* Are sensitive queries monitored?
* Is masking sufficient for the threat being addressed?
* Should stronger techniques such as tokenization be considered?

Dynamic Data Masking should therefore be treated as one layer of protection rather than a replacement for database authorization.

---

## 2. Authorization and Masking

Masking is most effective when combined with strong identity and authorization controls.

A user with sufficient database privileges may still be able to retrieve the original value.

For that reason, I would evaluate:

* Microsoft Entra ID authentication
* Database roles
* Least privilege
* Administrative privileges
* Application identities
* Privileged-access processes
* Access reviews

The architecture should answer both:

**What data should this identity be allowed to access?**

and:

**When access is permitted, does the identity need to see the complete value?**

---

## 3. Sensitivity Classification and Labeling

Microsoft Purview can provide sensitivity classification and labeling capabilities for supported information and services.

For this scenario, example classifications could include:

* Public
* Internal
* Confidential
* Highly Confidential

The actual classification model should come from organizational policy rather than being created independently within the cloud platform.

### Classification Flow

A typical governance flow is:

**Data Identified → Classification Applied → Owner Validates → Protection Requirements Determined → Controls Enforced**

A classification such as **Confidential** might influence requirements for:

* Access
* Encryption
* Sharing
* Retention
* External distribution
* Monitoring
* DLP
* Incident handling

The exact controls would depend on organizational policy.

---

## 4. Classification Is Not Protection

A sensitivity label communicates how information should be treated.

It does not automatically guarantee that every required protection is operating correctly.

For example, I would still verify:

* Who has access
* Whether encryption is enabled
* Whether external sharing is allowed
* Whether retention is appropriate
* Whether sensitive activity is monitored
* Whether DLP controls are required
* Whether the label is being applied consistently

This distinction is important because classification provides **context for security decisions**.

---

## 5. Data Loss Prevention

Data masking and sensitivity classification can contribute to a broader DLP architecture, but they are not equivalent to DLP.

A broader DLP capability may evaluate sensitive information as it is:

* Stored
* Accessed
* Shared
* Transmitted
* Uploaded
* Downloaded
* Sent to external recipients

DLP policies can then detect or restrict activities that violate organizational data-handling requirements.

For this project, I would therefore describe masking and classification as **inputs to a broader data-protection and DLP strategy**, rather than claiming this module implements DLP itself.

---

## 6. Monitoring

Sensitive-data controls should generate appropriate evidence.

Depending on the architecture, I would evaluate monitoring for:

* Database administrative activity
* Access to sensitive tables
* Authentication failures
* Privilege changes
* Classification changes
* Label-policy changes
* DLP events where DLP is implemented
* Security exceptions

This helps demonstrate whether the controls are operating as intended.

---

## 7. Failure Considerations

### Masking Failure

If a masking rule is removed or incorrectly configured, sensitive values could become visible to identities that previously received masked results.

Configuration changes should therefore be controlled and monitored.

### Classification Failure

If information is incorrectly classified, downstream security controls may be inappropriate.

The governance process needs a way to:

* Correct classifications
* Handle false positives
* Resolve ownership questions
* Manage exceptions
* Review high-risk classifications

---

## 8. ISO 27001 Context

Data masking and classification can support organizational security requirements involving:

* Information classification
* Access control
* Protection of sensitive information
* Privacy
* Monitoring
* Risk treatment
* Secure information handling

The exact relationship to ISO/IEC 27001 should be determined from the organization's applicable version of the standard, ISMS scope, risk assessment, Statement of Applicability, policies, and control design.

I would not treat a Microsoft Purview label or an Azure SQL masking rule as a one-to-one implementation of an ISO 27001 control.

---

## Architecture Takeaway

For this scenario, I would treat data protection as a layered decision:

**Identify the Data → Classify It → Determine Who Needs Access → Determine What They Need to See → Apply Protection → Monitor Usage → Respond to Violations**

Dynamic Data Masking answers only part of that problem.

Sensitivity classification answers another part.

Identity, encryption, monitoring, governance, and potentially DLP complete the broader architecture.
