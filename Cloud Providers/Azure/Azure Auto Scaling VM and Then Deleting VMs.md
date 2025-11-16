# Azure VM Autoscaling Configuration

This document provides instructions for configuring autoscaling for Azure Virtual Machines within the **iso27001** resource group.

---

## Prerequisites

- Azure CLI installed  
- Logged in to Azure account  
- Existing VMs in the `iso27001` resource group  

---

## 1. Login to Azure

~~~bash
az login
~~~

---

## 2. Check Existing VMs

~~~bash
az vm list --resource-group iso27001 --output table
~~~

---

## 3. Create a VM Scale Set

~~~bash
az vmss create \
    --resource-group iso27001 \
    --name iso27001-scaleset \
    --image Ubuntu2204 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --instance-count 2
~~~

---

## 4. Add Scale-Out Rule (Scale Up)

~~~bash
az monitor autoscale rule create \
    --resource-group iso27001 \
    --autoscale-name iso27001-autoscale \
    --condition "Percentage CPU > 70 avg 5m" \
    --scale out 2
~~~

---

## 5. Add Scale-In Rule (Scale Down)

~~~bash
az monitor autoscale rule create \
    --resource-group iso27001 \
    --autoscale-name iso27001-autoscale \
    --condition "Percentage CPU < 30 avg 5m" \
    --scale in 1
~~~

---

## Additional Configuration Options

### Create a Scale Set from an Existing VM

If you want to create a scale set based on an existing VM, follow these steps.

### Step 1: Create an Image From the Existing VM

~~~bash
az vm deallocate --resource-group iso27001 --name yourExistingVM

az vm generalize --resource-group iso27001 --name yourExistingVM

az image create \
    --resource-group iso27001 \
    --name myVMImage \
    --source yourExistingVM
~~~

### Step 2: Create Scale Set Using the Custom Image

~~~bash
az vmss create \
    --resource-group iso27001 \
    --name iso27001-scaleset \
    --image myVMImage \
    --instance-count 2
~~~

---

## Deleting Resources

### Delete the Autoscale Settings

~~~bash
az monitor autoscale delete \
    --resource-group iso27001 \
    --name iso27001-autoscale
~~~

### Delete the VM Scale Set (If Needed)

~~~bash
az vmss delete \
    --resource-group iso27001 \
    --name iso27001-scaleset
~~~
