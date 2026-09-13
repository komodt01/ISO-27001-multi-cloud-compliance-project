# Google Cloud Logging, Monitoring, and Security Detection

## Purpose

This module demonstrates a Google Cloud logging and monitoring pattern using Cloud Logging, log sinks, logs-based metrics, and Cloud Monitoring alerting.

For this scenario, the objective is to collect security-relevant telemetry, create detection logic from selected events, and connect those detections to an operational response process.

The architecture pattern is:

**Google Cloud Activity → Cloud Logging → Detection / Export → Alert → Investigation / Response**

Logging alone does not provide a complete detection capability.

---

## 1. Log Export

A log sink can export selected logs to another destination for retention, analytics, or centralized processing.

Example:

```bash
gcloud logging sinks create security-sink \
    storage.googleapis.com/security-logs-bucket \
    --log-filter='resource.type="gce_instance" AND severity>=WARNING'
```

This example exports selected Compute Engine logs with severity `WARNING` or higher to a Cloud Storage bucket.

### Architecture Considerations

For a production environment, I would determine:

* Which logs require export
* Why the logs are being exported
* Required retention
* Destination ownership
* Encryption requirements
* Access control
* Cost
* Regulatory requirements
* Whether the data is also needed in a SIEM

A sink should exist because it supports a defined requirement, not simply because log export is technically possible.

---

## 2. Logs-Based Metrics

Cloud Logging can create metrics from matching log events.

Example:

```bash
gcloud logging metrics create sensitive-data-write-activity \
    --description="Metric for writes to the sensitive-data customers table" \
    --filter='resource.type="bigquery_resource" AND protoPayload.methodName="google.cloud.bigquery.v2.TableDataService.insertAll" AND protoPayload.resourceName="projects/PROJECT-ID/datasets/sensitive-data/tables/customers"'
```

This example is intended to detect write activity against a designated BigQuery table.

The original project described this as “sensitive data access,” but the `insertAll` operation represents data being written rather than general read access.

For that reason, I would describe the detection according to what the telemetry actually shows.

---

## 3. Define the Security Use Case

A write operation against a sensitive table is not automatically suspicious.

The security requirement might instead be:

* Detect unexpected writes
* Detect writes from unauthorized identities
* Detect unusually high write volume
* Detect activity outside expected processing windows
* Detect changes to protected datasets
* Detect access from unexpected services

The detection logic should reflect the actual risk being monitored.

---

## 4. Detection Context

For a production implementation, I would consider context such as:

* Principal identity
* Service account
* Resource
* Operation
* Source
* Time
* Frequency
* Success or failure
* Expected application behavior
* Dataset sensitivity

The more context available, the easier it is to distinguish legitimate business activity from activity requiring investigation.

---

## 5. Alerting Policy

A logs-based metric can feed a Cloud Monitoring alerting policy.

Conceptually:

```text
Cloud Logging
      │
      ▼
Logs-Based Metric
      │
      ▼
Cloud Monitoring Alert
      │
      ▼
Notification / Investigation
```

The alert policy should be created using a supported Cloud Monitoring configuration and validated against the current metric resource type and environment.

The original example used an alpha CLI command:

```bash
gcloud alpha monitoring policies create \
    --policy-from-file=policy.json
```

For a production environment, I would prefer currently supported interfaces and versioned infrastructure-as-code or API-based deployment rather than relying on an alpha command.

---

## 6. Example Detection Threshold

A threshold-based alert might trigger when a logs-based metric exceeds an expected level.

For example:

**Sensitive Table Write Activity > Expected Threshold → Alert**

The threshold should be based on:

* Normal workload behavior
* Business process
* Risk tolerance
* False-positive rate
* Investigation capacity

An arbitrary threshold should not be treated as production-ready detection logic.

---

## 7. Alert Response

The operational path should be defined before the alert is considered complete.

Conceptually:

**Detection → Alert → Investigation → Risk Decision → Response**

An analyst might determine:

* Which identity generated the activity?
* Was the activity expected?
* Was the identity authorized?
* What data was affected?
* Were other suspicious events present?
* Is containment required?

I would avoid automatically taking disruptive action without sufficient confidence and context.

---

## 8. Monitoring the Logging Architecture

The monitoring system itself can fail.

I would consider monitoring for:

* Log sink changes
* Missing expected telemetry
* Metric-definition changes
* Alert-policy changes
* Notification failures
* Permission changes
* Disabled audit logging

A detection architecture should be able to identify when its visibility has been degraded.

---

## 9. Identity and Access

Access to logging and monitoring resources should follow least privilege.

I would define:

* Who can create sinks
* Who can modify log filters
* Who can create metrics
* Who can change alert policies
* Who can query security logs
* Which automation identities can manage monitoring resources

Security telemetry may contain sensitive operational information and should itself be protected.

---

## 10. Retention and Cost

Logging architecture should balance security and compliance requirements against cost.

Relevant decisions include:

* Which logs remain in Cloud Logging
* Which logs are exported
* How long they are retained
* Which events require rapid query capability
* Which events can be archived

The goal is to retain the evidence required for security and compliance without creating unnecessary operational overhead.

---

## 11. Evidence

Useful evidence from this architecture could include:

* Logging configuration
* Sink definition
* Export destination
* Logs-based metric definition
* Alerting policy
* Notification configuration
* Alert history
* IAM configuration
* Investigation records

This shows the full path from telemetry to operational response.

---

## ISO 27001 Context

This architecture can support organizational security requirements involving:

* Event logging
* Monitoring
* Detection
* Administrative accountability
* Incident response
* Evidence preservation

The exact relationship to ISO/IEC 27001 depends on the organization's ISMS scope, risk assessment, Statement of Applicability, architecture, and control design.

Cloud Logging or Cloud Monitoring should not be treated as proof of ISO 27001 compliance by themselves.

---

## Architecture Takeaway

For this scenario, I would use:

**Required Security Event → Google Cloud Telemetry → Logging → Detection Logic → Metric → Alert → Investigation → Response → Evidence**

The important question is not:

**“Did we create a log sink?”**

It is:

**“Are we collecting the right event, detecting the right behavior, and routing it to the right response process?”**
