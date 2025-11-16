# Google Cloud DLP – Inspection Templates & Jobs

This module demonstrates how to create a DLP inspection template and run a DLP inspection job across Cloud Storage using the Google Cloud CLI.

---

## 1. Create a DLP Inspection Template

~~~bash
gcloud dlp inspection-templates create \
    --location=global \
    --template-id=sensitive-data-template \
    --display-name="Sensitive Data Template" \
    --description="Template to identify PII and sensitive data" \
    --info-types=CREDIT_CARD_NUMBER,PHONE_NUMBER,EMAIL_ADDRESS,US_SOCIAL_SECURITY_NUMBER
~~~

---

## 2. Create a DLP Inspection Job

The job scans a Cloud Storage bucket and stores findings in BigQuery.

Replace:

- `PROJECT-ID`
- `BUCKET-NAME`

before running.

~~~bash
gcloud dlp jobs create inspect \
    --project=PROJECT-ID \
    --location=global \
    --inspect-template-name=projects/PROJECT-ID/locations/global/inspectTemplates/sensitive-data-template \
    --storage-config-file-set-url="gs://BUCKET-NAME/*" \
    --action-save-findings-output-table-project=PROJECT-ID \
    --action-save-findings-output-table-dataset=dlp_findings \
    --action-save-findings-output-table-table=findings
~~~
