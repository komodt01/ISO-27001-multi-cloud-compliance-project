# Azure VM Scale Set (VMSS) Deployment with Autoscaling

This document deploys a secure VM Scale Set (VMSS) with a load balancer, NSG, VNet, autoscaling rules, and supporting network resources.  
It aligns with ISO 27001 operational security and capacity planning controls.

---

## 1. Set Variables

~~~bash
RESOURCE_GROUP="security-tools-rg"
LOCATION="eastus"

VNET_NAME="security-vnet"
SUBNET_NAME="security-subnet"
NSG_NAME="security-tools-nsg"

PUBLIC_IP_NAME="security-monitor-ip"
LB_NAME="security-monitor-lb"

VMSS_NAME="security-monitor-vmss"
VM_IMAGE="Ubuntu2204"   # Ubuntu 22.04 LTS
ADMIN_USER="azureuser"

INSTANCE_COUNT=2     # Initial VM count
MAX_INSTANCES=5      # Max VMSS autoscale size
MIN_INSTANCES=1      # Min VMSS autoscale size
SCALE_CPU_THRESHOLD=75
~~~

---

## 2. Create a Resource Group

~~~bash
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
~~~

---

## 3. Create the Virtual Network and Subnet

~~~bash
az network vnet create \
    --resource-group $RESOURCE_GROUP \
    --name $VNET_NAME \
    --address-prefix 10.0.0.0/16 \
    --subnet-name $SUBNET_NAME \
    --subnet-prefix 10.0.1.0/24
~~~

---

## 4. Create a Network Security Group

~~~bash
az network nsg create \
    --resource-group $RESOURCE_GROUP \
    --name $NSG_NAME
~~~

---

## 5. Add NSG Rule for SSH (Port 22)

~~~bash
az network nsg rule create \
    --resource-group $RESOURCE_GROUP \
    --nsg-name $NSG_NAME \
    --name AllowSSH \
    --protocol Tcp \
    --direction Inbound \
    --priority 1000 \
    --source-address-prefixes "*" \
    --source-port-ranges "*" \
    --destination-address-prefixes "*" \
    --destination-port-ranges 22 \
    --access Allow
~~~

---

## 6. Create a Public IP Address

~~~bash
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --name $PUBLIC_IP_NAME \
    --sku Standard \
    --allocation-method Static
~~~

---

## 7. Create a Load Balancer (Required for VMSS)

~~~bash
az network lb create \
    --resource-group $RESOURCE_GROUP \
    --name $LB_NAME \
    --sku Standard \
    --public-ip-address $PUBLIC_IP_NAME \
    --backend-pool-name ${VMSS_NAME}-backend-pool
~~~

---

## 8. Create the VM Scale Set (VMSS)

~~~bash
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
~~~

---

## 9. Enable Autoscaling

~~~bash
az monitor autoscale create \
    --resource-group $RESOURCE_GROUP \
    --name "${VMSS_NAME}-autoscale" \
    --target $VMSS_NAME \
    --min-count $MIN_INSTANCES \
    --max-count $MAX_INSTANCES \
    --count $INSTANCE_COUNT
~~~

---

## 10. Add Scale-Out Rule (CPU > threshold)

~~~bash
az monitor autoscale rule create \
    --resource-group $RESOURCE_GROUP \
    --autoscale-name "${VMSS_NAME}-autoscale" \
    --condition "Percentage CPU > $SCALE_CPU_THRESHOLD avg 5m" \
    --scale out 1
~~~

---

## 11. Add Scale-In Rule (CPU < threshold)

~~~bash
az monitor autoscale rule create \
    --resource-group $RESOURCE_GROUP \
    --autoscale-name "${VMSS_NAME}-autoscale" \
    --condition "Percentage CPU < 30 avg 5m" \
    --scale in 1
~~~
