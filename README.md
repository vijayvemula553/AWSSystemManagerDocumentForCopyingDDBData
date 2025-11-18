# DynamoDB Record Cloner via Lambda + SSM

## Overview

Clone DynamoDB records from one table to another using:

- Lambda (`CloneDynamoDBRecords`)  
- IAM role for Lambda  
- SSM Automation Document (`Clone-DynamoDB-Records`)  

Supports filtering by:

- ISO/epoch timestamps (`DateField`, `AfterTimestamp`, `BeforeTimestamp`)  
- Primary keys (`PrimaryKeys`)  
- Attribute filters (`AttributeFilters`)  

---

## Features

- Fully deployable via CloudFormation  
- SSM Automation Document automatically references the Lambda ARN  
- Flexible filtering options: date, primary keys, and attributes  
- Supports large table scans with batching  

---

## Deployment Steps

### Step 1: Deploy CloudFormation Stack

```bash
aws cloudformation deploy \
  --template-file clone-dynamodb-stack.yaml \
  --stack-name CloneDynamoDBStack \
  --capabilities CAPABILITY_NAMED_IAM
```

### Step 2: Resources Created
After deploying the CloudFormation stack, the following resources will be created:

- **Lambda function:** `CloneDynamoDBRecords`  
- **IAM Role for Lambda:** `CloneDynamoDBRecords-ExecutionRole`  
- **SSM Automation Document:** `Clone-DynamoDB-Records`  
- **IAM Role for SSM:** `CloneDynamoDB-SSMRole` (optional cross-account)

###  Step3: SSM Automation Document Parameters

| Parameter          | Type   | Description |
|------------------ |--------|-------------|
| `SourceTable`      | String | DynamoDB table to copy from |
| `TargetTable`      | String | DynamoDB table to copy to |
| `DateField`        | String | Optional timestamp field |
| `AfterTimestamp`   | String | Optional ISO/epoch timestamp (after) |
| `BeforeTimestamp`  | String | Optional ISO/epoch timestamp (before) |
| `PrimaryKeys`      | String | Optional JSON array of primary keys |
| `AttributeFilters` | String | Optional JSON key/value filters |

> Pass `PrimaryKeys` and `AttributeFilters` as **stringified JSON**.

### Examples for lambda input
1. Copy all records after a timestamp
  ``` {
  "SourceTable": "Orders",
  "TargetTable": "OrdersBackup",
  "DateField": "updatedAt",
  "AfterTimestamp": "2025-01-01T00:00:00Z"
}
```

2. Copy records matching attributes
```{
  "SourceTable": "Orders",
  "TargetTable": "OrdersBackup",
  "DateField": "updatedAt",
  "AfterTimestamp": "2025-01-01T00:00:00Z",
  "AttributeFilters": "{\"status\":\"DELIVERED\",\"region\":\"US\"}"
}
```

3. Copy specific primary keys
   ```
   {
  "SourceTable": "Orders",
  "TargetTable": "OrdersBackup",
  "PrimaryKeys": "[{\"orderId\":\"12345\"},{\"orderId\":\"67890\"}]"
}
```



