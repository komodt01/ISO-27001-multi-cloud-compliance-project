# GCP Security Monitoring Firewall Rules

## Purpose

This module demonstrates firewall rules used to restrict network access to a security-monitoring workload in Google Cloud.

The example allows internal SSH and HTTPS traffic to instances identified as security-monitoring workloads.

The purpose is to demonstrate **explicit network access and workload targeting**, not to treat firewall rules as a complete network-security architecture or as proof of ISO 27001 compliance.

---

## Architecture Context

For this scenario, the basic access path is:

**Approved Internal Network → VPC Firewall Rule → Security Monitoring Workload**

The firewall rules define:

* Source network
* Direction
* Protocol
* Port
* Target workload
* Priority

The objective is to avoid unnecessary broad access to the monitoring workload.

---

## Prerequisites

Replace environment-specific values such as:

* `PROJECT-ID`
* `security-monitoring-server`
* `us-central1-a`
* `security-network`

before using the commands.

The example assumes:

* The VPC already exists.
* The monitoring VM already exists.
* The `10.0.0.0/16` range represents an approved internal network for the lab.
* The workload does not require unrestricted Internet-based administrative access.

---

## 1. Allow Internal SSH

```bash id="d9iwpg"
gcloud compute firewall-rules create allow-ssh-internal \
    --project PROJECT-ID \
    --network security-network \
    --action allow \
    --direction ingress \
    --rules tcp:22 \
    --source-ranges 10.0.0.0/16 \
    --priority 1000 \
    --target-tags security-monitoring
```

This limits SSH access to the specified internal address range rather than allowing connections from any Internet source.

### Production Consideration

`10.0.0.0/16` is illustrative.

In a production environment, I would restrict administrative access to the smallest practical management boundary.

I would also evaluate whether direct SSH is required at all or whether a controlled administrative-access mechanism would provide a stronger architecture.

---

## 2. Allow Internal HTTPS

```bash id="0xvwk4"
gcloud compute firewall-rules create allow-https-internal \
    --project PROJECT-ID \
    --network security-network \
    --action allow \
    --direction ingress \
    --rules tcp:443 \
    --source-ranges 10.0.0.0/16 \
    --priority 1010 \
    --target-tags security-monitoring
```

HTTPS access is similarly restricted to the approved internal source range.

In production, the required source should reflect the actual consumers of the monitoring service rather than automatically allowing the entire internal network.

---

## 3. Identify the Target Workload

The firewall rules use the `security-monitoring` network tag to identify applicable instances.

```bash id="85nwb1"
gcloud compute instances add-tags security-monitoring-server \
    --project PROJECT-ID \
    --tags security-monitoring \
    --zone us-central1-a
```

This associates the monitoring VM with the firewall rules using the matching target tag.

---

## 4. Targeting Considerations

Network tags provide a straightforward way to target firewall rules, but the targeting model itself is part of the security architecture.

For a production environment, I would evaluate whether firewall targeting should use:

* Network tags
* Service accounts
* Hierarchical firewall policies
* Organizational policy
* Other centrally governed network controls

The appropriate choice depends on the organization's GCP architecture and governance model.

---

## 5. Network Segmentation

Creating separate firewall rules does not automatically mean the workload is effectively segmented.

I would validate:

* What sources can reach the workload
* What destinations the workload can reach
* Whether other firewall rules create broader access
* Whether administrative access is separated from application traffic
* Whether egress requires restriction
* Whether firewall changes are governed and logged

The effective network path matters more than the existence of an individual firewall rule.

---

## 6. Monitoring

Firewall and network activity should feed appropriate security telemetry.

Depending on the environment, I would evaluate:

* Firewall rule changes
* VPC flow visibility
* Administrative access
* Denied connections
* Unexpected source networks
* Changes to workload tags or identities

This is especially important because changing the target or source definition can alter the effective security boundary without modifying the application itself.

---

## 7. ISO 27001 Context

Network filtering and segmentation can support organizational security requirements involving:

* Network security
* Access control
* Segregation
* Monitoring
* Secure administration
* Change management

The exact relationship to ISO/IEC 27001 depends on the organization's ISMS scope, risk assessment, Statement of Applicability, architecture, and control design.

A firewall rule is one technical enforcement mechanism within that broader control environment.

---

## Architecture Takeaway

For this scenario, the important question is not simply whether port 22 or 443 is allowed.

I would evaluate:

**Who needs connectivity → From where → To what workload → On which protocol → For what purpose → How narrowly can it be allowed → How is the access monitored**

That turns firewall configuration into an architecture decision based on trust boundaries and required communication paths.
