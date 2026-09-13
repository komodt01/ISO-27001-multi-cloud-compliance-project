# Azure VM Scale Set and Autoscaling

## Purpose

This module demonstrates the use of an Azure Virtual Machine Scale Set and autoscaling to support workload availability and capacity management.

Autoscaling can contribute to resilience by increasing or decreasing compute capacity based on workload demand. It does not by itself provide high availability, disaster recovery, or ISO 27001 compliance.

---

## Architecture Context

For this scenario, the basic architecture is:

**Workload Demand → Azure Monitor Metric → Autoscale Policy → VM Scale Set → Capacity Adjustment**

The objective is to maintain sufficient compute capacity while avoiding unnecessary resources when demand decreases.

For a production environment, I would evaluate autoscaling together with:

* Availability-zone design
* Load balancing
* Application statelessness
* Health monitoring
* Failure detection
* Backup and recovery
* Deployment strategy
* Capacity limits
* Cost controls

---

## Prerequisites

* Azure CLI installed
* Authentication to an approved Azure environment
* Existing `iso27001` resource group
* Appropriate Azure permissions
* Network architecture defined for the workload

---

## 1. Authenticate to Azure

```bash id="rca03i"
az login
```

For an enterprise environment, authentication should follow the organization's approved identity and privileged-access model rather than relying on unmanaged administrative credentials.

---

## 2. Review Existing Virtual Machines

```bash id="yq5pfm"
az vm list \
    --resource-group iso27001 \
    --output table
```

This provides visibility into existing compute resources before creating the scale set.

---

## 3. Create the VM Scale Set

```bash id="p8dshs"
az vmss create \
    --resource-group iso27001 \
    --name iso27001-scaleset \
    --image Ubuntu2204 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --instance-count 2
```

The instance count of two is illustrative.

Production capacity should be determined from workload requirements, expected traffic, failure tolerance, availability objectives, and cost.

---

## 4. Configure Autoscaling

Create an autoscale configuration for the VM Scale Set.

```bash id="qq0kpr"
az monitor autoscale create \
    --resource-group iso27001 \
    --resource iso27001-scaleset \
    --resource-type Microsoft.Compute/virtualMachineScaleSets \
    --name iso27001-autoscale \
    --min-count 2 \
    --max-count 6 \
    --count 2
```

The minimum, maximum, and default instance counts are example values.

In production, I would determine these limits from capacity testing and business requirements.

---

## 5. Configure Scale-Out

The following example adds capacity when average CPU utilization exceeds 70 percent for five minutes.

```bash id="1hqvif"
az monitor autoscale rule create \
    --resource-group iso27001 \
    --autoscale-name iso27001-autoscale \
    --condition "Percentage CPU > 70 avg 5m" \
    --scale out 2
```

CPU is used here because it provides a simple demonstration metric.

A production workload may require more meaningful scaling signals such as:

* Request rate
* Queue depth
* Application latency
* Concurrent sessions
* Custom application metrics

---

## 6. Configure Scale-In

The following example removes capacity when average CPU utilization falls below 30 percent for five minutes.

```bash id="f0sq2m"
az monitor autoscale rule create \
    --resource-group iso27001 \
    --autoscale-name iso27001-autoscale \
    --condition "Percentage CPU < 30 avg 5m" \
    --scale in 1
```

Scale-in should be more conservative than scale-out when removing instances could interrupt active processing.

For a production system, I would validate application behavior during instance termination and consider appropriate cooldown and connection-draining behavior.

---

## 7. Resilience Considerations

Autoscaling addresses **capacity**, not every availability problem.

For example, adding more instances does not protect against:

* Regional failure
* Application defects
* Identity-service failure
* Database failure
* Network failure
* Dependency failure
* Corrupt deployment
* Misconfiguration

A production resilience architecture would therefore combine autoscaling with other availability and recovery controls based on business requirements.

---

## 8. Creating a Scale Set from a Custom Image

Where standardized workload images are required, a VM can be generalized and used to create an image.

```bash id="mxw3id"
az vm deallocate \
    --resource-group iso27001 \
    --name yourExistingVM

az vm generalize \
    --resource-group iso27001 \
    --name yourExistingVM

az image create \
    --resource-group iso27001 \
    --name myVMImage \
    --source yourExistingVM
```

The image can then be used by the scale set:

```bash id="kbnm6p"
az vmss create \
    --resource-group iso27001 \
    --name iso27001-scaleset \
    --image myVMImage \
    --instance-count 2
```

In a production environment, I would prefer a controlled image-management process with patching, vulnerability management, versioning, and approval rather than manually creating images from arbitrary VMs.

---

## 9. Resource Cleanup

Delete the autoscale configuration when it is no longer required:

```bash id="olhg6r"
az monitor autoscale delete \
    --resource-group iso27001 \
    --name iso27001-autoscale
```

Delete the scale set when the lab resources are no longer required:

```bash id="5yub44"
az vmss delete \
    --resource-group iso27001 \
    --name iso27001-scaleset
```

For a lab environment, removing unused resources also helps control cloud cost.

---

## ISO 27001 Context

Autoscaling and VM Scale Sets can support broader organizational requirements involving availability, operational resilience, capacity management, and continuity.

The required architecture should be determined by:

* Business impact
* Availability requirements
* Recovery objectives
* Workload criticality
* Risk assessment
* Cost
* Organizational resilience requirements

Deploying a VM Scale Set does not by itself establish compliance or resilience.

---

## Architecture Takeaway

Autoscaling is a **capacity-management mechanism**, not a complete resilience strategy.

For this scenario, I would evaluate:

**Business Availability Requirement → Workload Behavior → Scaling Signal → Capacity Limits → Failure Behavior → Recovery Strategy → Monitoring**

That keeps the technology tied to the business requirement rather than treating autoscaling itself as the architecture.
