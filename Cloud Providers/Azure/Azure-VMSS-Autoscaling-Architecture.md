# Azure VMSS Autoscaling Architecture

## Purpose

This module demonstrates an Azure Virtual Machine Scale Set architecture with supporting network resources, load balancing, and autoscaling.

The example focuses on **capacity management and workload resilience** within the broader multi-cloud security and compliance project.

Autoscaling can support availability objectives, but it does not by itself provide high availability, disaster recovery, or ISO 27001 compliance.

---

## Architecture Flow

For this scenario:

**Client Traffic → Load Balancer → VM Scale Set → Application Instances**

Azure Monitor metrics provide the scaling signal:

**VMSS Metrics → Autoscale Policy → Scale Out / Scale In**

Supporting resources include:

* Resource group
* Virtual network
* Subnet
* Network Security Group
* Public IP for the example load-balanced endpoint
* Azure Load Balancer
* VM Scale Set
* Autoscale policy

---

## 1. Set Variables

```bash id="11xfqw"
RESOURCE_GROUP="security-tools-rg"
LOCATION="eastus"

VNET_NAME="security-vnet"
SUBNET_NAME="security-subnet"
NSG_NAME="security-tools-nsg"

PUBLIC_IP_NAME="security-monitor-ip"
LB_NAME="security-monitor-lb"

VMSS_NAME="security-monitor-vmss"
VM_IMAGE="Ubuntu2204"
ADMIN_USER="azureuser"

INSTANCE_COUNT=2
MAX_INSTANCES=5
MIN_INSTANCES=1
SCALE_CPU_THRESHOLD=75
```

These values are illustrative.

Production sizing, region selection, network ranges, and scaling limits should be based on business and technical requirements.

---

## 2. Create the Resource Group

```bash id="c1ckh1"
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

---

## 3. Create the Virtual Network and Subnet

```bash id="iqb8qk"
az network vnet create \
    --resource-group $RESOURCE_GROUP \
    --name $VNET_NAME \
    --address-prefix 10.0.0.0/16 \
    --subnet-name $SUBNET_NAME \
    --subnet-prefix 10.0.1.0/24
```

The network ranges are examples and would need to fit the organization's broader Azure and hybrid network architecture.

---

## 4. Create the Network Security Group

```bash id="xj6s5e"
az network nsg create \
    --resource-group $RESOURCE_GROUP \
    --name $NSG_NAME
```

The NSG should allow only traffic required by the workload.

---

## 5. Administrative Access

The original lab configuration allowed SSH from any source.

I would not use that pattern for a production environment.

If direct SSH is required for a lab, the source should be restricted to a known management network.

Example:

```bash id="pg8vmb"
az network nsg rule create \
    --resource-group $RESOURCE_GROUP \
    --nsg-name $NSG_NAME \
    --name AllowSSHFromManagement \
    --protocol Tcp \
    --direction Inbound \
    --priority 1000 \
    --source-address-prefixes 10.10.0.0/24 \
    --source-port-ranges "*" \
    --destination-address-prefixes "*" \
    --destination-port-ranges 22 \
    --access Allow
```

The CIDR above is illustrative.

For production administration, I would evaluate controlled alternatives such as Azure Bastion, private management connectivity, or another approved privileged-access path rather than exposing SSH directly to the Internet.

---

## 6. Create a Public IP for the Example Endpoint

```bash id="m3yrz5"
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --name $PUBLIC_IP_NAME \
    --sku Standard \
    --allocation-method Static
```

A public endpoint is used here to demonstrate a load-balanced architecture.

It should not be assumed that every VMSS requires public exposure.

For internal services, I would evaluate private load balancing and private connectivity instead.

---

## 7. Create the Load Balancer

```bash id="qv1v1a"
az network lb create \
    --resource-group $RESOURCE_GROUP \
    --name $LB_NAME \
    --sku Standard \
    --public-ip-address $PUBLIC_IP_NAME \
    --backend-pool-name ${VMSS_NAME}-backend-pool
```

The load balancer distributes traffic across the VMSS instances.

A production design would also need to define:

* Frontend ports
* Backend ports
* Health probes
* Load-balancing rules
* Session behavior
* Application health criteria

---

## 8. Create the VM Scale Set

```bash id="vrfzqv"
az vmss create \
    --resource-group $RESOURCE_GROUP \
    --name $VMSS_NAME \
    --image $VM_IMAGE \
    --admin-username $ADMIN_USER \
    --vnet-name $VNET_NAME \
    --subnet $SUBNET_NAME \
    --instance-count $INSTANCE_COUNT \
    --load-balancer $LB_NAME \
    --nsg $NSG_NAME \
    --upgrade-policy-mode Automatic \
    --generate-ssh-keys
```

The initial instance count of two is illustrative.

For production, I would also evaluate:

* Availability-zone placement
* Image management
* Patch management
* Managed identity
* Disk encryption
* Monitoring
* Backup requirements
* Vulnerability management
* Application deployment
* Health probes
* Failure behavior

---

## 9. Configure Autoscaling

```bash id="vlzvxx"
az monitor autoscale create \
    --resource-group $RESOURCE_GROUP \
    --name "${VMSS_NAME}-autoscale" \
    --target $VMSS_NAME \
    --min-count $MIN_INSTANCES \
    --max-count $MAX_INSTANCES \
    --count $INSTANCE_COUNT
```

The minimum and maximum values should ultimately reflect tested capacity and availability requirements.

---

## 10. Configure Scale-Out

```bash id="ihaxd6"
az monitor autoscale rule create \
    --resource-group $RESOURCE_GROUP \
    --autoscale-name "${VMSS_NAME}-autoscale" \
    --condition "Percentage CPU > $SCALE_CPU_THRESHOLD avg 5m" \
    --scale out 1
```

CPU provides a simple demonstration metric.

Depending on the workload, I might instead evaluate:

* Request volume
* Queue depth
* Application latency
* Concurrent sessions
* Custom application metrics

The scaling signal should represent actual workload pressure rather than simply using CPU because it is readily available.

---

## 11. Configure Scale-In

```bash id="7etdzn"
az monitor autoscale rule create \
    --resource-group $RESOURCE_GROUP \
    --autoscale-name "${VMSS_NAME}-autoscale" \
    --condition "Percentage CPU < 30 avg 5m" \
    --scale in 1
```

Scale-in needs particular attention because removing capacity can affect active workloads.

I would validate:

* Connection draining
* Active sessions
* Long-running processing
* Application state
* Cooldown periods
* Minimum safe capacity

---

## Security Considerations

### Identity

Administrative and workload identities should follow least privilege.

For workload access to Azure resources, I would prefer managed identities where supported rather than embedding credentials in the VM image or application configuration.

### Network

The workload should expose only required ports.

Administrative access should use a controlled management path rather than broad Internet exposure.

### Monitoring

I would monitor:

* VM health
* Scaling events
* Authentication activity
* NSG changes
* VMSS configuration changes
* Application health
* Resource utilization

### Configuration

VM images should come from a controlled image-management process with appropriate:

* Hardening
* Patch management
* Vulnerability management
* Versioning
* Approval

---

## Resilience Considerations

Autoscaling protects primarily against **capacity pressure**.

It does not automatically protect against:

* Availability-zone failure
* Regional failure
* Application defects
* Database failure
* Identity failure
* Network failure
* Dependency failure
* Bad deployments

Those risks require additional resilience patterns based on the application's business requirements.

---

## ISO 27001 Context

The architecture can support broader organizational requirements involving:

* Capacity management
* Availability
* Operational resilience
* Secure configuration
* Access control
* Monitoring
* Change management

The relationship to ISO/IEC 27001 depends on the organization's ISMS scope, risk assessment, Statement of Applicability, architecture, and control design.

Deploying a VM Scale Set or autoscale policy does not itself establish ISO 27001 compliance.

---

## Architecture Takeaway

The architecture decision is broader than simply enabling autoscaling.

For this scenario, I would evaluate:

**Business Availability Requirement → Workload Architecture → Network Exposure → Health Monitoring → Scaling Signal → Capacity Limits → Failure Behavior → Recovery Strategy**

Autoscaling then becomes one part of the resilience architecture rather than being treated as the resilience architecture itself.
