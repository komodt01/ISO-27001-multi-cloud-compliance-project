# AWS Data Protection — Masking and Encryption

## Purpose

This module demonstrates two different data-protection concepts in AWS:

* Data masking through an application or processing function
* Encryption at rest for data stored in DynamoDB

These controls address different risks.

**Masking** limits unnecessary exposure of sensitive values during processing or presentation.

**Encryption at rest** protects stored data through cryptographic controls and key management.

Neither capability should be treated as a complete Data Loss Prevention (DLP) solution or as proof of ISO 27001 compliance.

---

## Architecture Context

For this scenario, the data-protection flow can be viewed as:

**Sensitive Data → Processing / Masking → Application Use**

and:

**Application Data → DynamoDB → KMS-Protected Encryption at Rest**

The controls should be selected based on the data, its use, the threat being addressed, and business requirements.

Encryption does not eliminate the need for masking, and masking does not eliminate the need for encryption.

---

## 1. Data Masking Function

The project uses a Lambda function as an example processing point where masking logic could be applied before sensitive information is returned or passed to another system.

Example deployment command:

```bash id="unw4fo"
aws lambda update-function-code \
  --function-name DataMaskingFunction \
  --zip-file fileb://masking-function.zip
```

This command only deploys the Lambda function package.

The actual protection provided depends on the masking logic contained within the function.

### Architecture Considerations

Before using a masking function in production, I would determine:

* Which data elements require masking?
* Which users or systems should receive masked versus unmasked values?
* Is masking reversible or irreversible?
* Where should the original value remain accessible?
* Does the application require the complete value for processing?
* How is authorization enforced before unmasked data is returned?
* Are requests and access to sensitive values logged?
* What happens if the masking function fails?

For highly sensitive data, I would also evaluate whether tokenization or another data-protection technique is more appropriate than simple masking.

---

## 2. DynamoDB Encryption with AWS KMS

The following example configures server-side encryption for a DynamoDB table using AWS KMS.

```bash id="dd47n2"
aws dynamodb update-table \
  --table-name sensitive-table \
  --sse-specification Enabled=true,SSEType=KMS,KMSMasterKeyId=KEY-ID
```

The KMS key identifier is a placeholder and should be replaced with the appropriate key for the environment.

### Architecture Considerations

For production use, I would also evaluate:

* KMS key ownership
* Key policies
* IAM permissions
* Separation of administrative responsibilities
* Key rotation requirements
* Audit logging
* Recovery requirements
* Cross-account access
* Regulatory requirements
* Application dependencies on the key

Using KMS-backed encryption provides stronger control over key governance, but the security outcome still depends on who can use the key and who can access the underlying data.

---

## 3. Why Encryption and Masking Are Different

Encryption protects data by transforming it cryptographically.

Authorized applications can decrypt the data when appropriate permissions and key access are available.

Masking changes what a user or consuming system sees.

For example, an application may store:

`123-45-6789`

but return:

`***-**-6789`

to a user who does not require the complete value.

The organization may therefore need both controls:

**Encryption protects stored data.**

**Masking reduces unnecessary exposure during use.**

---

## 4. Access Control

Neither masking nor encryption replaces authorization.

I would still apply least privilege to:

* DynamoDB access
* Lambda invocation
* KMS key usage
* Administrative access
* Application identities

A user who can legitimately decrypt and retrieve every record may still have excessive access even though the database is encrypted.

Data protection therefore needs to work with IAM rather than being treated as an isolated cryptographic control.

---

## 5. Monitoring

Relevant activity should be observable through appropriate AWS telemetry.

Examples include:

* KMS key usage
* DynamoDB administrative activity
* Lambda configuration changes
* IAM changes
* Access failures
* Security-relevant application events

Monitoring is particularly important when sensitive data can be returned in an unmasked form.

---

## 6. Failure Considerations

### Masking Failure

If the masking function fails, the architecture should not automatically return the original sensitive value.

For a sensitive-data workflow, I would generally prefer the system to fail safely rather than expose unmasked data because the protection mechanism was unavailable.

The exact behavior would depend on the business process and availability requirements.

### KMS Failure

Applications that depend on KMS-protected resources may be affected if key access is denied or the expected key configuration is unavailable.

That makes key-management architecture part of application resilience as well as data protection.

---

## 7. ISO 27001 Context

Masking and encryption can support broader organizational requirements involving:

* Protection of sensitive information
* Cryptographic controls
* Access control
* Secure processing
* Monitoring
* Risk treatment

Their specific role in an ISO 27001-aligned environment depends on the organization's risk assessment, information-classification requirements, Statement of Applicability, architecture, and control design.

Deploying Lambda, DynamoDB, or KMS does not by itself demonstrate ISO 27001 compliance.

---

## Architecture Takeaway

The architecture decision is not simply whether sensitive data is encrypted.

I would evaluate protection throughout the data lifecycle:

**Who can access the data → Where it is stored → How it is protected at rest → How it is exposed during processing → What users can see → How access is monitored → What happens when a protection mechanism fails**

For this scenario, encryption, masking, IAM, and monitoring work together as layers of data protection rather than as interchangeable controls.
