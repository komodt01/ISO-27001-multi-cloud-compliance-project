# Data Masking and DLP (AWS)

This module demonstrates how to implement basic data masking and encryption controls in AWS using Lambda, DynamoDB, and KMS.

## 1. Update Lambda Function for Data Masking

```bash
aws lambda update-function-code \
  --function-name DataMaskingFunction \
  --zip-file fileb://masking-function.zip

2. Enable DynamoDB Encryption with KMS
aws dynamodb update-table \
  --table-name sensitive-table \
  --sse-specification Enabled=true,SSEType=KMS,KMSMasterKeyId=KEY-ID
