# Azure Activity Logs and Diagnostic Settings Configuration

This document configures Azure Activity Logs, NSG logs, Firewall logs, VM diagnostics, and Key Vault auditing for centralized ISO 27001–aligned monitoring using Azure Log Analytics.

---

## 1. Create a Log Analytics Workspace

~~~bash
az monitor log-analytics workspace create \
    --resource-group SECURITY-TOOLS-RG \
    --workspace-name iso27001-analytics \
    --location eastus \
    --sku PerGB2018 \
    --retention-time 90
~~~

---

## 2. Configure Azure Activity Logs → Log Analytics

### Get the Log Analytics workspace ID

~~~bash
WORKSPACE_ID=$(az monitor log-analytics workspace show \
    --resource-group SECURITY-TOOLS-RG \
    --workspace-name iso27001-analytics \
    --query id -o tsv)
~~~

### Enable Activity Log collection at subscription level

~~~bash
az monitor diagnostic-settings create \
    --name iso27001-activity-logs \
    --resource $(az account show --query id -o tsv) \
    --logs '[{"category":"Administrative","enabled":true},
             {"category":"Security","enabled":true},
             {"category":"ServiceHealth","enabled":true},
             {"category":"Alert","enabled":true},
             {"category":"Recommendation","enabled":true},
             {"category":"Policy","enabled":true},
             {"category":"Autoscale","enabled":true},
             {"category":"ResourceHealth","enabled":true}]' \
    --workspace $WORKSPACE_ID
~~~

---

## 3. Configure Diagnostic Settings for Specific Resources

---

### **Network Security Group (NSG)**

Get the NSG resource ID:

~~~bash
NSG_ID=$(az network nsg show \
    --resource-group SECURITY-TOOLS-RG \
    --name iso27001-security-nsg \
    --query id -o tsv)
~~~

Enable NSG diagnostic logs:

~~~bash
az monitor diagnostic-settings create \
    --name iso27001-nsg-diag \
    --resource $NSG_ID \
    --logs '[{"category":"NetworkSecurityGroupEvent","enabled":true},
             {"category":"NetworkSecurityGroupRuleCounter","enabled":true}]' \
    --workspace $WORKSPACE_ID
~~~

---

### **Azure Firewall (optional)**

Get Firewall resource ID:

~~~bash
FIREWALL_ID=$(az network firewall show \
    --resource-group SECURITY-TOOLS-RG \
    --name ngfw1 \
    --query id -o tsv 2>/dev/null)
~~~

If Firewall exists, enable diagnostics:

~~~bash
if [ ! -z "$FIREWALL_ID" ]; then
    az monitor diagnostic-settings create \
        --name iso27001-fw-diag \
        --resource $FIREWALL_ID \
        --logs '[{"category":"AzureFirewallApplicationRule","enabled":true},
                 {"category":"AzureFirewallNetworkRule","enabled":true},
                 {"category":"AzureFirewallDnsProxy","enabled":true}]' \
        --metrics '[{"category":"AllMetrics","enabled":true}]' \
        --workspace $WORKSPACE_ID
fi
~~~

---

## 4. Configure Diagnostic Settings for Key Vault (optional)

Get Key Vault ID:

~~~bash
KV_ID=$(az keyvault show \
    --name security-keyvault \
    --resource-group SECURITY-TOOLS-RG \
    --query id -o tsv 2>/dev/null)
~~~

Enable if exists:

~~~bash
if [ ! -z "$KV_ID" ]; then
    az monitor diagnostic-settings create \
        --name iso27001-kv-diag \
        --resource $KV_ID \
        --logs '[{"category":"AuditEvent","enabled":true},
                 {"category":"AzurePolicyEvaluationDetails","enabled":true}]' \
        --metrics '[{"category":"AllMetrics","enabled":true}]' \
        --workspace $WORKSPACE_ID
fi
~~~

---

## 5. Configure Diagnostic Settings for Virtual Machine

Get VM ID:

~~~bash
VM_ID=$(az vm show \
    --resource-group SECURITY-TOOLS-RG \
    --name security-monitor-vm \
    --query id -o tsv 2>/dev/null)
~~~

Enable diagnostics if VM exists:

~~~bash
if [ ! -z "$VM_ID" ]; then
    az monitor diagnostic-settings create \
        --name iso27001-vm-diag \
        --resource $VM_ID \
        --metrics '[{"category":"AllMetrics","enabled":true}]' \
        --workspace $WORKSPACE_ID
fi
~~~
