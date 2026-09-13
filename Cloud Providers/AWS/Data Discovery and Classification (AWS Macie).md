# AWS Data Discovery and Classification with Amazon Macie

## Purpose

This module demonstrates how Amazon Macie can be used to discover and classify sensitive data stored in Amazon S3.

The capability can support data governance and protection activities within an ISO 27001-aligned security program by improving visibility into where sensitive information is stored.

Macie does not establish ISO 27001 compliance by itself. Classification results need to feed broader processes for data ownership, access control, remediation, retention, monitoring, and risk management.

---

## Architecture Context

For this scenario, I would use Macie as a **detective data-security capability**.

The basic flow is:

**S3 Data → Macie Discovery → Sensitive Data Findings → Review / Risk Decision → Remediation**

The important outcome is not simply identifying sensitive data.

The organization needs to determine:

* What type of data was identified?
* Where is it stored?
* Who owns it?
* Who can access it?
* Is the storage location approved?
* Is encryption appropriate?
* Is the data required to remain there?
* Does the finding represent a policy or regulatory issue?
* What remediation is required?

---

## 1. Enable Amazon Macie

```bash id="a0n2je"
aws macie2 enable-macie
```

Enabling Macie makes the service available for sensitive-data discovery and security analysis.

In a production environment, I would also evaluate:

* Account and organizational coverage
* Regional requirements
* IAM permissions
* Finding destinations
* Monitoring integration
* Cost
* Data-discovery scope

---

## 2. Create a Custom Data Identifier

Macie includes managed data identifiers, but custom identifiers can be created when an organization needs to detect data patterns specific to its environment.

For this example, a custom identifier is created for a U.S. Social Security Number pattern.

```bash id="c4yj2m"
aws macie2 create-custom-data-identifier \
    --name "CustomerSSN" \
    --regex "[0-9]{3}-[0-9]{2}-[0-9]{4}" \
    --description "Identifies patterns matching US Social Security Numbers"
```

### Classification Consideration

A regex match should not automatically be treated as proof that the identified value is an actual Social Security Number.

The pattern could also match unrelated data.

In a production implementation, I would evaluate additional context, managed identifiers, keywords, exclusion patterns, and validation procedures to reduce false positives and improve classification confidence.

---

## 3. Create a Classification Job

The following example creates a one-time discovery job against a designated S3 bucket.

```bash id="z23zgz"
aws macie2 create-classification-job \
    --name "InitialDataDiscovery" \
    --s3-job-definition '{"bucketDefinitions":[{"accountId":"YOUR-ACCOUNT-ID","buckets":["sensitive-data-bucket"]}]}' \
    --job-type "ONE_TIME" \
    --sampling-percentage 100
```

This example uses 100% sampling for demonstration purposes.

In a production environment, the discovery strategy should consider:

* Number of buckets
* Data volume
* Data sensitivity
* Cost
* Business criticality
* Existing classification
* Regulatory requirements
* Frequency of change

Not every S3 object necessarily requires continuous full-content scanning.

---

## 4. Findings and Response

Discovery becomes useful when findings lead to an appropriate response.

For a sensitive-data finding, I would evaluate:

1. **Data classification**
   What information was identified?

2. **Location**
   Which account, bucket, and object contain the data?

3. **Ownership**
   Which business or application owner is responsible?

4. **Exposure**
   Who or what can access the data?

5. **Protection**
   Are encryption, access controls, retention, and monitoring appropriate?

6. **Business requirement**
   Is the sensitive data supposed to exist in this location?

7. **Remediation**
   Should access be restricted, the data moved, tokenized, masked, deleted, or otherwise protected?

---

## 5. Monitoring Integration

In an enterprise environment, I would not treat Macie as an isolated tool.

Relevant findings could feed:

* Security monitoring
* Incident investigation
* Data-governance workflows
* Risk-management processes
* Compliance evidence
* Remediation workflows

Automation may be appropriate for well-understood findings, but potentially destructive remediation should not occur solely because a classification engine produced a match.

---

## 6. ISO 27001 Context

Sensitive-data discovery can provide evidence supporting an organization's broader information-security management program.

Relevant areas can include:

* Information classification
* Data handling
* Access control
* Monitoring
* Data protection
* Risk assessment
* Compliance obligations

The exact relationship to ISO 27001 controls depends on the organization's:

* ISMS scope
* Risk assessment
* Statement of Applicability
* Data-classification policy
* Regulatory requirements
* Control design

The architecture should therefore start with the organization's data-protection requirements and use Macie where it is an appropriate technical capability for meeting those requirements.

---

## Architecture Takeaway

Amazon Macie provides visibility into sensitive data stored in S3, but discovery is only the first step.

For this scenario, I would treat the complete control path as:

**Discover → Classify → Validate → Determine Exposure → Assess Risk → Remediate → Monitor**

That turns sensitive-data discovery into part of an enterprise data-protection process rather than treating deployment of a cloud security service as the control itself.
