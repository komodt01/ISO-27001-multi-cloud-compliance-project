# Google Cloud Environment Prerequisites

This document identifies the basic Google Cloud environment prerequisites used to support the multi-cloud compliance project.

It is not an implementation of ISO 27001 controls by itself. The security and compliance architecture is documented separately within the project.

---

## 1. Install the Google Cloud CLI

Example installation for Linux:

```bash id="7y5e5c"
curl https://sdk.cloud.google.com | bash
```

Reload the shell after installation:

```bash id="j0w0kr"
exec -l $SHELL
```

Confirm installation:

```bash id="9tdg36"
gcloud version
```

---

## 2. Initialize Google Cloud CLI

```bash id="fr7c85"
gcloud init
```

Initialization can authenticate the user and establish the default Google Cloud configuration.

For an enterprise environment, authentication should use the organization's approved identity and access model.

---

## 3. Verify the Active Configuration

Review the current configuration:

```bash id="a1lzt6"
gcloud config list
```

Verify the active project:

```bash id="hh45k6"
gcloud config get-value project
```

If required, select the intended project:

```bash id="7l8jpf"
gcloud config set project PROJECT-ID
```

Verifying the project context is important when administrative users have access to multiple Google Cloud projects.

---

## 4. Authentication and Identity

For production environments, I would expect access to follow organizational identity standards.

Depending on the architecture, this could include:

* Centralized identity
* MFA
* Least privilege
* IAM roles
* Controlled privileged access
* Workload identities
* Service-account governance

Long-lived service-account keys should be avoided where stronger workload-identity mechanisms are available.

Automation identities should have defined ownership, purpose, and permissions.

---

## 5. Security Considerations

Before using CLI-based automation or assessment commands, I would verify:

* The active identity has only the required permissions.
* The intended project is selected.
* Administrative credentials are not stored in source control.
* Shared credentials are avoided.
* Workload identities are used where appropriate.
* Administrative activity is captured through appropriate logging.
* Privileged access follows the organization's governance process.

---

## 6. Project Scope

These prerequisites only establish access to the Google Cloud environment.

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

Those controls should be evaluated based on the organization's scope, risk assessment, Statement of Applicability, and Google Cloud architecture rather than assuming that use of a particular cloud service automatically establishes ISO 27001 compliance.
