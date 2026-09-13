# AWS Security Monitoring Infrastructure Example

## Purpose

This document provides an example AWS infrastructure pattern for hosting security monitoring tooling within the multi-cloud compliance project.

The purpose is to demonstrate how a dedicated monitoring workload could be isolated and protected in AWS. Deploying this infrastructure does not by itself establish ISO 27001 compliance.

In a production environment, I would first evaluate whether the monitoring requirement is better met through AWS-native services, an enterprise SIEM/SaaS platform, or a dedicated monitoring workload before introducing a persistent EC2 server.

---

## Architecture Assumptions

For this example:

* A VPC and subnet already exist.
* The monitoring workload is deployed into a controlled subnet.
* Administrative access is restricted to approved management networks.
* Security telemetry is collected separately through appropriate AWS logging services.
* The instance does not require direct inbound Internet access.
* IAM permissions follow least privilege.
* Encryption, logging, patching, backup, vulnerability management, and recovery requirements would need to be defined before production use.

---

## 1. Create the Security Group

Create a dedicated security group for the monitoring workload.

```bash id="6sj7cm"
aws ec2 create-security-group \
  --group-name security-tools-sg \
  --description "Security group for security monitoring workload" \
  --vpc-id vpc-xxxxxxxx
```

The security group should allow only the traffic required by the monitoring architecture.

---

## 2. Administrative Access

If SSH is required for this example, access should be limited to an approved management network rather than exposed to the Internet.

```bash id="o2r40a"
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxxxxxxxxxxxxxx \
  --protocol tcp \
  --port 22 \
  --cidr 10.0.0.0/16
```

For a production AWS environment, I would evaluate AWS Systems Manager Session Manager or another controlled administrative-access mechanism instead of maintaining direct inbound SSH access.

The example CIDR is illustrative and should not be copied into a production environment without validating the actual management network.

---

## 3. Create the Monitoring Instance

```bash id="r5c0qz"
aws ec2 run-instances \
  --image-id ami-xxxxxxxxxxxxxxxxx \
  --count 1 \
  --instance-type t3.large \
  --key-name security-key-pair \
  --security-group-ids sg-xxxxxxxxxxxxxxxxx \
  --subnet-id subnet-xxxxxxxxxxxxxxxxx \
  --block-device-mappings '[{"DeviceName":"/dev/sda1","Ebs":{"VolumeSize":100,"DeleteOnTermination":false}}]' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=security-monitoring-server},{Key=Purpose,Value=SecurityMonitoring}]'
```

The instance type and storage size are examples rather than production sizing recommendations.

Production sizing would depend on factors such as:

* Telemetry volume
* Processing requirements
* Retention requirements
* Availability requirements
* Monitoring software
* Performance requirements
* Cost constraints

---

## 4. Validate Instance State

After provisioning, verify that the instance reaches the expected state.

```bash id="styyxc"
aws ec2 wait instance-running \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

Provisioning success does not mean the monitoring capability is operational. Additional validation would be required for the operating system, monitoring software, telemetry ingestion, access controls, logging, patching, backup, and alerting.

---

## 5. External Connectivity

The original lab design allowed an Elastic IP to be associated with the monitoring server.

For a production security architecture, I would avoid assigning a public IP unless there were a documented requirement for direct external connectivity.

Where possible, administrative and service connectivity should use private network paths or controlled management services.

If a public endpoint is required, the associated exposure and compensating controls would need to be explicitly evaluated.

---

## 6. Security Controls

A production version of this workload would require additional controls beyond instance creation.

### Identity

* Least-privilege IAM role
* Controlled administrative access
* Avoidance of unnecessary persistent credentials
* Logging of administrative activity

### Network

* Restricted ingress
* Controlled egress
* Appropriate subnet placement
* Defined trust boundaries
* Network telemetry where required

### Data Protection

* EBS encryption
* Appropriate key management
* Protection of monitoring data
* Defined retention requirements

### Monitoring

* AWS API activity logging
* Instance and operating-system telemetry
* Security alerts
* Monitoring of the monitoring infrastructure itself

### Vulnerability and Configuration Management

* Approved base image
* Patch management
* Vulnerability scanning
* Configuration baseline
* Exception management

### Resilience

* Backup requirements
* Recovery procedures
* Monitoring-data recovery considerations
* Availability requirements based on the criticality of the monitoring function

---

## 7. ISO 27001 Context

This infrastructure can support security capabilities relevant to an ISO 27001-aligned security program, particularly around monitoring, access control, configuration management, operational security, and evidence collection.

However, ISO 27001 compliance cannot be established by deploying an EC2 instance or any individual AWS service.

The organization would still need to determine:

* ISMS scope
* Applicable risks
* Required controls
* Control ownership
* Policies and procedures
* Evidence requirements
* Exceptions
* Control effectiveness
* Statement of Applicability

The AWS architecture should support those organizational requirements rather than treating cloud services as compliance controls by themselves.

---

## Architecture Takeaway

The important architectural decision is not how to launch an EC2 instance.

It is determining **whether a dedicated monitoring workload is needed, where it belongs, how it is accessed, what telemetry it processes, how it is protected, and how its operation supports the organization's broader security and compliance requirements.**
