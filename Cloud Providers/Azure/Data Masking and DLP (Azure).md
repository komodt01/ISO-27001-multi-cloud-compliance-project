# Data Masking and DLP (Azure)

This module demonstrates how to configure basic data masking, labeling, and data protection in Azure using SQL Database, Azure Information Protection, and PowerShell.

## 1. Enable SQL Database Dynamic Data Masking

~~~bash
az sql db update \
  --resource-group rg-name \
  --server server-name \
  --name db-name \
  --dynamic-data-masking-policy '{"rules":[{"column":"SSN","masking":"Number"},{"column":"CreditCard","masking":"Credit Card"}]}'
~~~

---

## 2. Create Information Protection Label via PowerShell  
*(Azure CLI does not fully support this — PowerShell is required)*

### Example PowerShell command:

~~~powershell
New-AIPLabel -Title "Confidential" -EncryptionEnabled $true
~~~
