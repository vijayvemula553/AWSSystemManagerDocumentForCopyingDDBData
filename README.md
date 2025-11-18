DynamoDB Record Cloner via Lambda + SSM
Overview

CloudFormation template to clone DynamoDB records using:

Lambda (CloneDynamoDBRecords)

IAM role for Lambda

SSM Automation Document (Clone-DynamoDB-Records)

Supports filtering by:

Timestamps (DateField + AfterTimestamp / BeforeTimestamp)

Primary keys (PrimaryKeys)

Attribute filters (AttributeFilters)

Deployment
aws cloudformation deploy \
  --template-file clone-dynamodb-stack.yaml \
  --stack-name CloneDynamoDBStack \
  --capabilities CAPABILITY_NAMED_IAM


Then execute the SSM document via AWS Systems Manager → Automation → Clone-DynamoDB-Records.

SSM Parameters
Parameter	Type	Description
SourceTable	String	DynamoDB table to copy from
TargetTable	String	DynamoDB table to copy to
DateField	String	Optional timestamp field
AfterTimestamp	String	Optional ISO/epoch timestamp (after)
BeforeTimestamp	String	Optional ISO/epoch timestamp (before)
PrimaryKeys	String	Optional JSON array of primary keys
AttributeFilters	String	Optional JSON key/value filters
Examples

Copy all after timestamp

{
  "SourceTable": "Orders",
  "TargetTable": "OrdersBackup",
  "DateField": "updatedAt",
  "AfterTimestamp": "2025-01-01T00:00:00Z"
}


Copy by attributes

{
  "SourceTable": "Orders",
  "TargetTable": "OrdersBackup",
  "AttributeFilters": "{\"status\":\"DELIVERED\",\"region\":\"US\"}"
}


Copy by primary keys

{
  "SourceTable": "Orders",
  "TargetTable": "OrdersBackup",
  "PrimaryKeys": "[{\"orderId\":\"12345\"},{\"orderId\":\"67890\"}]"
}

Notes

Supports ISO or epoch timestamps

Batch writing via batch_writer()

Empty filters copy all items (optionally filtered by date)

Cross-account execution possible via CloneDynamoDB-SSMRole
