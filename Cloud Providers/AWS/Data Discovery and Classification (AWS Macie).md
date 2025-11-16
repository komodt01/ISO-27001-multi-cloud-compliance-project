# Data Discovery and Classification (AWS Macie)

This module demonstrates how to enable Macie, create custom data identifiers, and run discovery classification jobs aligned to ISO 27001 Annex A data protection controls.

---

## 1. Enable Macie

~~~bash
aws macie2 enable-macie
~~~

---

## 2. Create a Custom Data Identifier

~~~bash
aws macie2 create-custom-data-identifier \
    --name "CustomerSSN" \
    --regex "[0-9]{3}-[0-9]{2}-[0-9]{4}" \
    --description "Identifies US Social Security Numbers"
~~~

---

## 3. Start a Classification Job

~~~bash
aws macie2 create-classification-job \
    --name "InitialDataDiscovery" \
    --s3-job-definition '{"bucketDefinitions":[{"accountId":"YOUR-ACCOUNT-ID","buckets":["sensitive-data-bucket"]}]}' \
    --job-type "ONE_TIME" \
    --sampling-percentage 100
~~~
