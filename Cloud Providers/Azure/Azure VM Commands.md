# Azure Security Tools Deployment

This document provides ISO 27001–aligned Azure CLI commands for creating a secure resource group, virtual network, NSG, and monitoring VM for security operations.

---

## 1. Create a Resource Group

~~~bash
az group create \
    --name security-tools-rg \
    --location eastus
~~~

---

## 2. Create a Virtual Network

~~~bash
az network vnet create \
    --resource-group security-tools-rg \
    --name security-vnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name security-subnet \
    --subnet-prefix 10.0.1.0/24
~~~

---

## 3. Create a Network Security Group

~~~bash
az network nsg create \
    --resource-group security-tools-rg \
    --name security-tools-nsg
~~~

---

## 4. Add SSH Rule to NSG

~~~bash
az network nsg rule create \
    --resource-group security-tools-rg \
    --nsg-name security-tools-nsg \
    --name AllowSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
~~~

---

## 5. Create a Public IP Address

~~~bash
az network public-ip create \
    --resource-group security-tools-rg \
    --name security-monitor-ip \
    --allocation-method Static
~~~

---

## 6. Create a Network Interface

~~~bash
az network nic create \
    --resource-group security-tools-rg \
    --name security-monitor-nic \
    --vnet-name security-vnet \
    --subnet security-subnet \
    --network-security-group security-tools-nsg \
    --public-ip-address security-monitor-ip
~~~

---

## 7. Create the Monitoring VM

~~~bash
az vm create \
    --resource-group security-tools-rg \
    --name security-monitor-vm \
    --nics security-monitor-nic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --size Standard_D2s_v3 \
    --os-disk-size-gb 100 \
    --generate-ssh-keys
~~~
