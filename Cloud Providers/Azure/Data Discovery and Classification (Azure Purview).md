# Azure Purview / Data Governance Setup

This document provides ISO 27001–aligned Azure CLI steps for establishing a data governance foundation using Azure Purview (Microsoft Purview).  
Note: Some steps require Azure Portal or REST API configuration, as Purview onboarding is not fully CLI-driven.

---

## 1. Create a Resource Group for Data Governance

~~~bash
az group create \
    --name data-governance-rg \
    --location eastus
~~~

---

## 2. Create a Purview Account  
*(Requires additional permissions; some configuration steps must be completed in the Azure Portal)*

~~~bash
az purview account create \
    --name purview-account \
    --resource-group data-governance-rg \
    --location eastus
~~~

---

## 3. Register Data Sources (REST API Only)

Azure Purview data source registration cannot be fully performed via Azure CLI.  
Registration typically requires:

- Azure Portal  
- REST API calls  
- Service Principal authentication  
- Proper role assignments (Purview Data Curator / Data Source Administrator)

### Example REST API registration flow (conceptual)

~~~bash
# Authenticate SP (example workflow)
az account get-access-token --resource https://purview.azure.net

# Example placeholder POST request to register an Azure Storage source
curl -X POST \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"name": "mystorage", "properties": {"endpoint": "<STORAGE-ENDPOINT>"}}' \
  https://<purview-account-name>.purview.azure.com/catalog/api/datasources?api-version=2021-07-01
~~~

*(This example will not work without replacing values and configuring permissions.)*

---

## Notes

- Purview onboarding often requires Portal-based setup  
- Enterprise deployments typically integrate:  
  - Azure Storage  
  - Azure SQL  
  - ADLS Gen2  
  - Power BI  
- Role assignments (Data Reader, Curator, Administrator) are essential  
- For ISO 27001 alignment, Purview maps primarily to:  
  - **A.8.2 – Information Classification**  
  - **A.12.4 – Logging and Monitoring**  
  - **A.18.1 – Compliance with Legal Requirements**

