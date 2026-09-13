# Google Cloud Data Discovery and Classification

## Purpose

This module demonstrates how Google Cloud Sensitive Data Protection can be used to inspect data stored in Cloud Storage for sensitive information and record findings for analysis.

The capability can support data discovery, classification, governance, and risk-management activities within an ISO 27001-aligned security program.

Sensitive-data discovery does not by itself establish compliance or determine whether identified data represents a policy violation.

---

## Architecture Context

For this scenario, the basic flow is:

**Cloud Storage → Sensitive Data Inspection → Findings → BigQuery → Review / Risk Decision → Remediation**

The inspection capability identifies potential sensitive information.

The organization still needs to determine:

* Is the finding accurate?
* Is the data expected in this location?
* Who owns the data?
* Who can access it?
* Is the storage location approved?
* Are the appropriate protections in place?
* Does retention remain justified?
* Does the finding require remediation?

---

## 1. Create an Inspection Template

An inspection template defines the sensitive-data patterns the organization wants to identify.

Example:

```bash id="qpnr5a"
gcloud dlp inspection-templates create \
    --location=global \
    --template-id=sensitive-data-template \
    --display-name="Sensitive Data Template" \
    --description="Template to identify PII and sensitive data" \
    --info-types=CREDIT_CARD_NUMBER,PHONE_NUMBER,EMAIL_ADDRESS,US_SOCIAL_SECURITY_NUMBER
```

For this scenario, the inspection looks for:

* Credit card numbers
* Phone numbers
* Email addresses
* U.S. Social Security Numbers

### Architecture Considerations

The selected information types should reflect actual organizational requirements.

I would determine them based on factors such as:

* Data-classification policy
* Regulatory obligations
* Business processes
* Data residency
* Privacy requirements
* Risk assessment

Scanning for every available information type may create unnecessary findings, processing overhead, and cost.

---

## 2. Create an Inspection Job

The following example scans objects in a Cloud Storage bucket and stores findings in BigQuery.

Replace:

* `PROJECT-ID`
* `BUCKET-NAME`

with the appropriate environment values.

```bash id="um27cw"
gcloud dlp jobs create inspect \
    --project=PROJECT-ID \
    --location=global \
    --inspect-template-name=projects/PROJECT-ID/locations/global/inspectTemplates/sensitive-data-template \
    --storage-config-file-set-url="gs://BUCKET-NAME/*" \
    --action-save-findings-output-table-project=PROJECT-ID \
    --action-save-findings-output-table-dataset=dlp_findings \
    --action-save-findings-output-table-table=findings
```

The BigQuery dataset and required permissions need to exist before findings can be written successfully.

---

## 3. Findings Are Security Data

The findings repository itself needs protection.

Sensitive-data findings can reveal information such as:

* Where sensitive data exists
* What type of sensitive information was detected
* Which resources contain it
* Patterns of sensitive-data storage

That makes the findings dataset potentially valuable to both defenders and attackers.

I would apply appropriate controls to the BigQuery findings dataset, including:

* Least-privilege IAM
* Logging
* Retention requirements
* Controlled administrative access
* Encryption and key-management requirements where appropriate
* Monitoring for unauthorized access

---

## 4. Validate Classification Results

An inspection result should not automatically be treated as proof of a policy violation.

Automated discovery can produce:

* False positives
* Expected sensitive-data findings
* Data already protected appropriately
* Findings requiring additional business context

For significant findings, I would validate:

1. Data type
2. Location
3. Data owner
4. Access
5. Business purpose
6. Existing protection
7. Regulatory requirements
8. Required remediation

---

## 5. Remediation

Discovery becomes valuable when it feeds a defined response process.

Depending on the finding, remediation could include:

* Restricting access
* Moving data
* Encrypting data
* Masking data
* Tokenizing data
* De-identifying data
* Correcting retention
* Removing unnecessary copies
* Deleting data when appropriate

The correct response depends on the business requirement and risk.

I would not automatically delete or transform information simply because the discovery service identified a sensitive-data pattern.

---

## 6. Monitoring and Governance

For an enterprise implementation, I would also monitor:

* Inspection-job failures
* Changes to inspection templates
* Changes to service identities
* Access to findings
* New sensitive-data locations
* Unresolved high-risk findings
* Exceptions
* Remediation status

This provides evidence that discovery is operating as part of a governance process rather than as an isolated scan.

---

## 7. ISO 27001 Context

Sensitive-data discovery can support security activities involving:

* Information classification
* Data protection
* Privacy
* Access control
* Monitoring
* Risk assessment
* Information lifecycle management

The specific relationship to ISO/IEC 27001 depends on the organization's applicable version of the standard, ISMS scope, risk assessment, Statement of Applicability, policies, and control design.

Using Google Cloud Sensitive Data Protection does not by itself establish conformity with an ISO 27001 control.

---

## Architecture Takeaway

For this scenario, I would treat sensitive-data discovery as a control process:

**Define Se**
