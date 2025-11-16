# Data Masking and DLP (Azure)

This module demonstrates how to configure basic data masking, data labeling, and protection features in Azure using **Azure SQL Database**, **Microsoft Purview / AIP**, and **PowerShell**.  
Some features (like labels) require PowerShell or the Purview/AIP portal because Azure CLI does not fully support them.

---

## 1. Enable SQL Database Dynamic Data Masking

Dynamic Data Masking (DDM) helps prevent unauthorized data exposure by masking sensitive fields at query time.

~~~bash
az sql db update \
    --resource-group rg-name \
    --server server-name \
    --name db-name \
    --dynamic-data-masking-policy '{
        "rules": [
            {"column": "SSN", "masking": "Number"},
            {"column": "CreditCard", "masking": "Credit Card"}
        ]
    }'
~~~

---

## 2. Create Information Protection Label (PowerShell Required)

Azure CLI does **not** support creating or managing AIP/MIP sensitivity labels.  
PowerShell or the Purview portal must be used.

### Example PowerShell Command

~~~powershell
New-AIPLabel -Title "Confidential" -EncryptionEnabled $true
~~~

---

## Notes

- Dynamic Data Masking applies at the query layer and does not modify stored data  
- Sensitivity labels require:  
  - Microsoft Purview Information Protection  
  - Azure AD Authentication  
  - Proper licensing depending on tenant  
- This module supports ISO 27001 controls:  
  - **A.8.2 – Information Classification**  
  - **A.9.4 – Access Control**  
  - **A.18.1 – Protection of Personally Identifiable Information**

