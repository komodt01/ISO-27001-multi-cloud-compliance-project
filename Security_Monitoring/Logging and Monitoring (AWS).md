# AWS Logging, Monitoring, and Sensitive Data Access Detection

## Purpose

This module demonstrates an AWS logging and monitoring pattern using CloudTrail, CloudWatch, metric-based detection, and alerting.

For this scenario, the objective is to create visibility into security-relevant AWS activity and demonstrate how selected events can be converted into actionable detections.

The architecture pattern is:

**AWS Activity → CloudTrail → Log Destination → Detection Logic → CloudWatch Alarm → Notification / Investigation**

Logging by itself does not provide detection. The telemetry must be collected, monitored, and connected to an operational response process.

---

## 1. Create a Multi-Region CloudTrail Trail

Example:

```bash
aws cloudtrail create-trail \
    --name security-trail \
    --s3-bucket-name cloudtrail-logs-bucket \
    --is-multi-region-trail \
    --enable-log-file-validation
```

Start logging:

```bash
aws cloudtrail start-logging \
    --name security-trail
```

This example assumes the S3 bucket already exists and has the appropriate CloudTrail permissions.

### Architecture Considerations

For a production environment, I would also evaluate:

* Organization-level trails
* Centralized log ownership
* S3 bucket access
* Encryption
* Retention
* Log-file validation
* Cross-account protection
* Administrative access
* Monitoring for logging configuration changes

The logging environment itself is a security asset and should be protected from unauthorized modification or deletion.

---

## 2. Management Events vs. Data Events

CloudTrail management events and data events serve different purposes.

Management events capture control-plane activity such as configuration and administrative API calls.

Some resource-level activity requires explicit **data-event** configuration.

For example, if the objective is to monitor access to specific DynamoDB data, I would verify that the appropriate CloudTrail data events are enabled for the required resources.

I would not assume that creating a trail automatically captures every sensitive-data access event required by the detection use case.

---

## 3. CloudWatch Logs Integration

If CloudWatch metric filters are going to evaluate CloudTrail events, those events need to be delivered to the relevant CloudWatch Logs log group.

Conceptually:

```text
CloudTrail
    │
    ├── S3 → Long-Term / Audit Storage
    │
    └── CloudWatch Logs → Detection / Alerting
```

The CloudTrail-to-CloudWatch Logs integration requires an appropriate log group and IAM role permitting CloudTrail to deliver events.

The exact implementation should be validated for the target AWS environment before treating the detection path as operational.

---

## 4. Create the Detection Log Group

Example:

```bash
aws logs create-log-group \
    --log-group-name data-access-logs
```

Creating the log group alone does not populate it.

The required telemetry source must be explicitly configured to deliver events to it.

---

## 5. Sensitive Data Access Detection

For this scenario, assume the organization wants visibility into access to a DynamoDB table named:

```text
sensitive-table
```

A CloudWatch metric filter can identify matching events once the required CloudTrail events are reaching the log group.

Example:

```bash
aws logs put-metric-filter \
    --log-group-name data-access-logs \
    --filter-name "SensitiveDataAccess" \
    --filter-pattern '{ $.eventName = "GetItem" && $.requestParameters.tableName = "sensitive-table" }' \
    --metric-transformations \
        metricName=SensitiveDataAccessCount,metricNamespace=SecurityMetrics,metricValue=1
```

This converts matching log events into a CloudWatch metric.

---

## 6. Detection Logic

A `GetItem` operation against a sensitive table is not inherently malicious.

The security question is whether the access is **unexpected or risky**.

In production, I would consider additional context such as:

* Identity
* Role
* Source
* Time
* Frequency
* Resource
* Expected application behavior
* Failed versus successful access
* Privileged activity
* Historical baseline

A useful detection should identify suspicious behavior rather than simply alert on normal business activity.

---

## 7. Create the CloudWatch Alarm

Example:

```bash
aws cloudwatch put-metric-alarm \
    --alarm-name SensitiveDataAccessAlert \
    --metric-name SensitiveDataAccessCount \
    --namespace SecurityMetrics \
    --statistic Sum \
    --period 300 \
    --evaluation-periods 1 \
    --threshold 10 \
    --comparison-operator GreaterThanThreshold \
    --alarm-actions arn:aws:sns:REGION:ACCOUNT-ID:security-alerts
```

The threshold of `10` events in five minutes is illustrative.

I would not select a production threshold without understanding normal workload behavior.

---

## 8. Alert Response

The alarm should feed a defined response process.

Conceptually:

**Detection → Alert → Investigation → Risk Decision → Response**

The response might include:

* Identify the principal responsible for the activity.
* Review related CloudTrail events.
* Determine whether the access was expected.
* Review IAM permissions.
* Determine whether sensitive information was exposed.
* Escalate if suspicious behavior is confirmed.

I would avoid automatically disabling an identity based only on this metric without additional validation.

---

## 9. Failure Paths

### CloudTrail Stops Logging

If CloudTrail logging is disabled or altered, the organization should detect the change.

Logging controls should therefore monitor the logging infrastructure itself.

### CloudWatch Delivery Fails

If CloudTrail events are not reaching CloudWatch Logs, the metric filter cannot detect the activity.

Telemetry health should be monitored so that a failed collection path does not silently eliminate detection coverage.

### Notification Fails

If the alarm fires but the SNS notification path fails, the detection may never reach an analyst.

The alert-delivery mechanism should therefore be tested and monitored.

---

## 10. Evidence

Useful evidence from this architecture could include:

* CloudTrail configuration
* Trail status
* Data-event configuration
* CloudWatch Logs configuration
* Metric-filter definition
* CloudWatch alarm configuration
* SNS notification configuration
* Alert history
* Investigation records

This demonstrates not just that logging exists, but that telemetry is connected to a detection and response process.

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

CloudTrail or CloudWatch should not be treated as proof of ISO 27001 compliance by themselves.

---

## Architecture Takeaway

For this scenario, the important architecture path is:

**Required Security Event → Telemetry Source → Collection → Detection Logic → Threshold → Alert → Investigation → Response → Evidence**

The objective is not simply to enable CloudTrail.

The objective is to ensure that the organization can detect and respond to the security activity that matters.
