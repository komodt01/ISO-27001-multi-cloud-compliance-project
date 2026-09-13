# Azure Environment Prerequisites

This document identifies the basic Azure environment prerequisites used to support the multi-cloud compliance project.

It is not an implementation of ISO 27001 controls by itself. The security and compliance architecture is documented separately within the project.

---

## 1. Azure CLI

Azure CLI can be used to inspect Azure resources, validate configurations, and support project automation.

Example installation for Debian/Ubuntu:

```bash id="8ag7x2"
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Confirm installation:

```bash id="v2v3la"
az version
```

---

## 2. Azure Authentication

Interactive authentication can be initiated with:

```bash id="c2txva"
az login
```

For an enterprise environment, authentication should use the organization's approved Microsoft Entra ID identity and access model.

I would expect administrative access to incorporate controls such as:

* MFA
* Least privilege
* Role-based access control
* Privileged Identity Management where appropriate
* Conditional Access where appropriate
* Controlled service and workload identities

Automation should use an approved non-human identity pattern rather than shared user credentials.

---

## 3. Verify Subscription Context

Before making configuration changes, verify the active subscription:

```bash id="t3xofb"
az account show --output table
```

Available subscriptions can be reviewed with:

```bash id="d3guk7"
az account list --output table
```

If required, select the intended subscription:

```bash id="vyu52e"
az account set \
    --subscription "SUBSCRIPTION-ID"
```

Verifying the subscription context is particularly important when administrative users have access to multiple Azure environments.

---

## 4. Security Considerations

Before using CLI-based automation or assessment scripts, I would verify:

* The authenticated identity has only the permissions required for the task.
* MFA and other applicable identity controls are enforced.
* Administrative privileges are not used for routine activity.
* Secrets and credentials are not stored in source control.
* Managed identities or workload identities are used where appropriate.
* Administrative activity is captured through appropriate Azure logging.
* Automation identities have defined ownership and purpose.

---

## 5. Project Scope

These prerequisites only establish access to the Azure environment.

ISO 27001 alignment requires additional architecture and control considerations such as:

* Identity and access management
* Logging and monitoring
* Data protection
* Network security
* Configuration governance
* Vulnerability management
* Incident response
* Resilience
* Evidence collection

Those controls should be evaluated based on the organization's scope, risk assessment, Statement of Applicability, and Azure architecture rather than assuming that use of a particular Azure service automatically establishes ISO 27001 compliance.
