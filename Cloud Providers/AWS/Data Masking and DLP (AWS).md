# Data Masking and DLP (AWS)

This module demonstrates how to implement basic data masking and encryption controls in AWS using Lambda, DynamoDB, and KMS. These examples support ISO 27001 Annex A controls for data protection and secure processing.

---

## 1. Update Lambda Function for Data Masking

Before running the update command, ensure the Lambda function exists (either from the AWS Console, CloudFormation, or Terraform) and that `masking-function.zip` contains the masking logic.

```bash
aws lambda update-function-code \
  --function-name DataMaskingFunction \
  --zip-file fileb://masking-function.zip
