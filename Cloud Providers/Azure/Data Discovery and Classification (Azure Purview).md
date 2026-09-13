# Data Discovery and Classification with Microsoft Purview

## Purpose

This module demonstrates how Microsoft Purview can support data discovery, classification, and governance within an Azure environment.

The capability can support an ISO 27001-aligned information security program by improving visibility into enterprise data, its classification, ownership, and use.

Microsoft Purview does not establish ISO 27001 compliance by itself. Discovery and classification need to connect to broader processes for data ownership, access control, retention, monitoring, risk management, and remediation.

---

## Architecture Context

For this scenario, I would use Purview as part of the organization's data-governance architecture.

The conceptual flow is:

**Data Sources → Purview Registration → Scan / Discovery → Classification → Data Catalog / Governance → Risk or Remediation Decision**

The important outcome is not simply creating a Purview account.

The organization needs to understand:

* What data exists?
* Where is it located?
* How is it classified?
* Who owns it?
* Who can access it?
* What regulatory or business requirements apply?
* Is it stored and handled appropriately?
* What action is required when inappropriate exposure is identified?

---

## 1. Create a Resource Group

The following example creates a resource group for the data-governance resources.

```bash id="n5h9s0"
az group create \
    --name data-governance-rg \
    --location eastus
```

The region is illustrative.

In a production environment, resource placement should consider organizational standards, service availability, data residency, regulatory requirements, and architecture dependencies.

---

## 2. Create the Purview Resource

Example:

```bash id="i53ivv"
az purview account create \
    --name purview-account \
    --resource-group data-governance-rg \
    --location eastus
```

Creating the resource establishes the service foundation but does not create a functioning enterprise data-governance program.

Additional configuration is required for:

* Data-source registration
* Permissions
* Collections and governance structure
* Scanning
* Classification
* Ownership
* Data stewardship
* Monitoring
* Remediation processes

Some configuration may require portal, API, or other supported management interfaces depending on the capability being configured.

---

## 3. Register Data Sources

Purview becomes useful when the organization's relevant data sources are brought into the governance scope.

Potential sources could include:

* Azure Storage
* Azure Data Lake Storage
* Azure SQL
* Other databases
* Analytics platforms
* Business intelligence platforms
* Supported non-Azure data sources

The appropriate scope depends on the organization's architecture and data-governance requirements.

### Access Considerations

Data-source registration and scanning should use controlled identities and least-privilege access.

I would define:

* Who can register sources
* Who can configure scans
* Which identity performs scanning
* What permissions that identity receives
* Who can modify classifications
* Who owns discovered data
* How administrative activity is audited

---

## 4. Discovery and Classification

Once sources are registered, scanning can be used to identify and classify data.

For this scenario, I would treat classification as the beginning of a governance decision rather than the final control.

A classification result should lead to questions such as:

1. Is the classification accurate?
2. Is this data expected in this location?
3. Who owns the data?
4. Who currently has access?
5. Does the data require additional protection?
6. Are retention requirements being met?
7. Does the location create residency or regulatory concerns?
8. Should the data be masked, encrypted, moved, restricted, or deleted?

---

## 5. Classification Accuracy

Automated classification is useful, but I would not assume every classification is correct.

Depending on the data type and business context, classification may require:

* Validation
* Custom classification rules
* Business metadata
* Data-owner review
* False-positive handling
* Exception processes

A mature data-governance process needs a way to resolve incorrect or ambiguous classifications rather than blindly applying controls based on automated labels.

---

## 6. Integration with Data Protection

Discovery alone does not protect data.

Purview findings and classifications should inform other controls such as:

* IAM
* Encryption
* Key management
* Masking
* Tokenization
* DLP
* Retention
* Data lifecycle management
* Monitoring
* Incident response

This creates a broader control path:

**Discover → Classify → Validate → Determine Ownership → Evaluate Exposure → Apply Protection → Monitor**

---

## 7. Monitoring and Governance

I would also establish monitoring around the governance capability itself.

Relevant areas include:

* Administrative changes
* Data-source registration
* Scan failures
* Permission changes
* Classification changes
* Coverage gaps
* Data sources that have not been scanned
* Exceptions
* Remediation status

This provides evidence that the governance process is operating rather than merely that the technology exists.

---

## 8. ISO 27001 Context

Microsoft Purview can support information-security activities involving:

* Information classification
* Data ownership
* Access control
* Data protection
* Monitoring
* Retention
* Compliance obligations
* Risk management

The exact ISO/IEC 27001 relationship should be determined from the organization's applicable version of the standard, ISMS scope, risk assessment, Statement of Applicability, policies, and control design.

I would avoid treating a Purview feature as a one-to-one implementation of an ISO 27001 control.

The technology provides capabilities that can support the organization's control objectives.

---

## Architecture Takeaway

The architectural value of Microsoft Purview is not simply creating a catalog of data.

For this scenario, I would use data discovery and classification to connect:

**Data → Ownership → Sensitivity → Access → Protection Requirement → Risk → Governance Action**

That turns data discovery into an input to security architecture and risk decisions rather than treating classification as an isolated compliance exercise.
