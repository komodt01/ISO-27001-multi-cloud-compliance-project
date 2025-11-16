# Azure Managed Disks

Managed disks provide improved reliability, security, and encryption capabilities compared to unmanaged disks.  
The commands below demonstrate how to create, attach, and use managed disks in Azure, aligned with ISO 27001 data protection controls.

---

## 1. Create a Managed Disk

~~~bash
az disk create \
    --resource-group SECURITY-TOOLS-RG \
    --name security-data-disk \
    --size-gb 1024 \
    --sku Premium_LRS \
    --encryption-type EncryptionAtRestWithPlatformKey \
    --location eastus
~~~

---

## 2. Attach the Disk to an Existing VM

~~~bash
az vm disk attach \
    --resource-group SECURITY-TOOLS-RG \
    --vm-name security-monitor-vm \
    --name security-data-disk
~~~

---

## 3. Create a New VM Using a Managed OS Disk and Data Disk

~~~bash
az vm create \
    --resource-group SECURITY-TOOLS-RG \
    --name security-vm \
    --image Canonical:0001-com-ubuntu-server-jammy:22_04-lts-gen2:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --size Standard_D2s_v3 \
    --os-disk-name security-os-disk \
    --os-disk-size-gb 128 \
    --data-disk-sizes-gb 1024 \
    --storage-sku Premium_LRS \
    --encryption-at-host true
~~~
