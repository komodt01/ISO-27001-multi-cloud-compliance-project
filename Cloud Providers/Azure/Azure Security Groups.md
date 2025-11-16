# Azure Network Security Group (NSG) Configuration

This document provides ISO 27001–aligned examples for creating and configuring Network Security Groups (NSGs) in Azure to enforce secure inbound traffic controls.

---

## 1. Create a Network Security Group

~~~bash
az network nsg create \
    --resource-group security-tools-rg \
    --name iso27001-security-nsg \
    --location eastus
~~~

---

## 2. Add Inbound Rule for SSH Access

~~~bash
az network nsg rule create \
    --resource-group security-tools-rg \
    --nsg-name iso27001-security-nsg \
    --name AllowSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow \
    --source-address-prefix 10.0.0.0/16
~~~

---

## 3. Add Inbound Rule for HTTPS Access

~~~bash
az network nsg rule create \
    --resource-group security-tools-rg \
    --nsg-name iso27001-security-nsg \
    --name AllowHTTPS \
    --protocol tcp \
    --priority 1010 \
    --destination-port-range 443 \
    --access allow \
    --source-address-prefix 10.0.0.0/16
~~~
