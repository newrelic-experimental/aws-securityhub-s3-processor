# newrelic-securitylake-s3-processor

### Subscriber Application

Subscriber application uses [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide) to create a Lambda function (including Lambda execution IAM Roles and related configurations), which gets triggered on SQS Queue. SQS Queue has the metadata of the S3 objects to read. Security lake inserts the records into this SQS Queue, when there is a new object put on the S3 bucket. The lambda function logic, when trigerred, reads the S3 objects (which are in Parquet format) and sends to New Relic over the defined endpoints. The lambda function does not filters out any data. Data is GZipped before its sent.

This subscriber application needs to be deployed only in one region. Use the region consolidation feature in AWS Security lake to consolidate all the data in one region's S3 bucket. 

Without the use of region consolidation, the direction to customers should be to deploy this subcriber application in every AWS region where they have security lake enabled.

This subscriber application needs to be deployed in customer's AWS account (not necessarily in the delegated security administrator, where the Security Lake is hosting the SQS), but at the time of subscriber creation, ensure to use the AWS account, where you wish to deploy the subcriber application (this Lambda function) as Security Lake will create necessary permissions on the SQS , S3 buckets and Cross account Roles.

![](./images/New%20Relic%20-%20Security%20Lake%20Subscriber.jpeg)

## SAM CLI installation

Refer [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-getting-started.html)

## Build of the SAM application

```
sam build
```

## Deploying the SAM application

Follow the prompts to enter the values

```
sam deploy --guided
```

* SecurityLakeSubscriberRoleArn : Go to the security lake > Subscriber > New Relic to see this
* SecurityLakeSQSArn : Go to the security lake > Subscriber > New Relic to see this. 
* ExternalID : Go to the security lake > Subscriber > New Relic to see this
* NRLicenseKey : New Relic license key

## NOTE

* Have only one Lambda function at a given point in the region, which is enabled to get trigerred for the SQS, for a given subscriber. You can ensure this by going to the Queue > Lambda Triggers