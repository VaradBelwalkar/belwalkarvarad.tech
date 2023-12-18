---
title: "AWS-CLI Commands"
date: 2023-11-09T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "Comprehensive list of AWS-CLI commands"
tags: ["VPC", "AWS", "services"]
categories: ["VPCs","AWS"]
---

**To configure AWS CLI**
```shell
aws configure
```
or you can configure specific one like this:

```shell
aws configure set aws_access_key_id <key>
aws configure set aws_secret_access_key <key>
aws configure set region us-east-1
aws configure set output json
```





**Create an S3 bucket:**

```shell
aws s3api create-bucket --bucket mybucket
```
---

**List S3 buckets:**

```shell
aws s3 ls
```
---

**Describe EC2 instances:**

```shell
aws ec2 describe-instances
```

---

**Start an EC2 instance:**

```shell
aws ec2 start-instances --instance-ids i-1234567890abcdef0
```

---

**List DynamoDB tables:**

```shell
aws dynamodb list-tables
```

---

**Create a DynamoDB table:**


```shell
aws dynamodb create-table --table-name MyTable --attribute-definitions AttributeName=MyKey,AttributeType=S --key-schema AttributeName=MyKey,KeyType=HASH --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
```

---


**List Lambda functions:**

```shell
aws lambda list-functions
```
---

**Create a Lambda function:**

```shell
aws lambda create-function --function-name MyFunction --runtime nodejs14.x --role arn:aws:iam::123456789012:role/MyRole --handler index.handler --code S3Bucket=mybucket,S3Key=mycode.zip
```

---

**Create an SNS topic:**

```shell
aws sns create-topic --name MyTopic
```

---

**List SNS topics:**

```shell
aws sns list-topics
```

---

**Create an SQS queue:**
```shell
aws sqs create-queue --queue-name MyQueue
```

---

**List SQS queues:**

```shell
aws sqs list-queues
```

---

**Create an IAM user:**

```shell
aws iam create-user --user-name MyUser
```

---

**List IAM users:**

```shell
aws iam list-users
```

---

**Create a Kinesis stream:**

```shell
aws kinesis create-stream --stream-name MyStream --shard-count 1
```

---

**List Kinesis streams:**

```shell
aws kinesis list-streams
```

---

**List RDS DB instances:**

```shell
aws rds describe-db-instances
```

---

**Create an RDS DB instance (requires more detailed configuration):**

```shell
aws rds create-db-instance ...
```

---

**List Elasticsearch domains:**

```shell
aws es list-domain-names 
```

---

**Create an Elasticsearch domain (requires more detailed configuration):**

```shell
aws es create-elasticsearch-domain ... 
```

---

**Upload a file to an S3 bucket:**

```shell
aws s3 cp /path/to/localfile s3://mybucket/myfile
```

---

**Download file from s3 bucket:**

```shell
aws s3 cp  s3://mybucket/myfile /path/to/folder
```


---

**List objects in an S3 bucket:**

```shell
aws s3 ls mybucket
```

---

**List CloudWatch alarms:**

```shell
aws cloudwatch describe-alarms
```

---

**Put metric data (simulating metrics):**

```shell
aws cloudwatch put-metric-data --namespace MyNamespace --metric-name MyMetric --value 1 
```

---

**List ECS clusters:**

```shell
aws ecs list-clusters
```

---

**Create an ECS cluster:**

```shell
aws ecs create-cluster --cluster-name MyCluster
```

---

**List EKS clusters:**

```shell
aws eks list-clusters
```

---

**Create an EKS cluster (requires more detailed configuration):**

```shell
aws eks create-cluster ...
```

---

**Create an SNS subscription for a topic:**

```shell
aws sns subscribe --topic-arn arn:aws:sns:us-east-1:123456789012:MyTopic --protocol email --notification-endpoint your@email.com
```

---

**Publish a message to an SNS topic:**

```shell
aws sns publish --topic-arn arn:aws:sns:us-east-1:123456789012:MyTopic --message "Hello, world!"
```

---

**Send a message to an SQS queue:**

```shell
aws sqs send-message --queue-url http://localhost:4576/queue/MyQueue --message-body "Hello, SQS!"
```

---

**Receive a message from an SQS queue:**

```shell
aws sqs receive-message --queue-url http://localhost:4576/queue/MyQueue
```

---

**List ElastiCache clusters:**

```shell
aws elasticache describe-cache-clusters
```

---

**Create an ElastiCache cluster (requires more detailed configuration):**

```shell
aws elasticache create-cache-cluster ...
```

---

**List KMS keys:**

```shell
aws kms list-keys
```

---

**Create a KMS key (requires more detailed configuration):**

```shell
aws kms create-key ...
```

---

**Create a CloudFormation stack (requires a CloudFormation template):**

```shell
aws cloudformation create-stack --stack-name MyStack --template-body file://my-template.json 
```

---

**List CloudFormation stacks:**

```shell
aws cloudformation list-stacks
```

---

**List hosted zones:**

```shell
aws route53 list-hosted-zones
```

---

**Create a hosted zone:**

```shell
aws route53 create-hosted-zone --name example.com --caller-reference example-001
```

---

**Describe ElastiCache clusters:**

```shell
aws elasticache describe-cache-clusters
```

---


**Create an ElastiCache replication group (requires more detailed configuration):**

```shell
aws elasticache create-replication-group ...
```

---

**List state machines:**

```shell
aws stepfunctions list-state-machines --endpoint-url http://localhost:4585
```

---

**Create a state machine (requires a Step Functions definition):**

```shell
aws stepfunctions create-state-machine --name MyStateMachine --definition file://state-machine-definition.json --role-arn arn:aws:iam::123456789012:role/MyRole
```

---

**List ElastiCache Redis clusters:**

```shell
aws elasticache describe-replication-groups
```

---

**Create an ElastiCache Redis cluster (cluster mode enabled):**

```shell
aws elasticache create-replication-group ... --replication-group-description "My Redis Cluster"
```

---

**List App Runner services:**

```shell
aws apprunner list-services
```

---

**Create an App Runner service (requires more detailed configuration):**

```shell
aws apprunner create-service ... --source-code file://apprunner-source-code.json
```

---

**List Pinpoint projects:**

```shell
aws pinpoint list-projects
```

---

**Create a Pinpoint project (requires more detailed configuration):**

```shell
aws pinpoint create-app ...
```

---

**Update an App Runner service:**

```shell
aws apprunner update-service --service-id MyServiceID --source-code file://updated-source-code.json
```

---

**Delete an App Runner service:**

```shell
aws apprunner delete-service --service-id MyServiceID
```

---

**List MediaConvert jobs:**

```shell
aws mediaconvert list-jobs
```

---

**Create a MediaConvert job (requires a job JSON configuration):**

```shell
aws mediaconvert create-job --cli-parameters file://mediaconvert-job.json
```

---

**List Fargate clusters:**

```shell
aws ecs list-clusters
```

---

**Create a Fargate cluster:**

```shell
aws ecs create-cluster --cluster-name MyFargateCluster --capacity-providers FARGATE
```

---

**Describe a state machine:**

```shell
aws stepfunctions describe-state-machine --state-machine-arn arn:aws:states:us-east-1:123456789012:stateMachine:MyStateMachine
```

---

**Delete a state machine:**

```shell
aws stepfunctions delete-state-machine --state-machine-arn arn:aws:states:us-east-1:123456789012:stateMachine:MyStateMachine
```

---

List Cognito user pools:

```shell
aws cognito-idp list-user-pools
```

---

**Create a Cognito user pool:**

```shell
aws cognito-idp create-user-pool --pool-name MyUserPool
```

---

**Describe Elasticsearch domains:**

```shell
aws es describe-elasticsearch-domain --domain-name MyElasticsearchDomain 
```

---

**Delete an Elasticsearch domain:**

```shell
aws es delete-elasticsearch-domain --domain-name MyElasticsearchDomain 
```

---

**List DataSync tasks:**

```shell
aws datasync list-tasks
```

---

**Create a DataSync task (requires more detailed configuration):**

```shell
aws datasync create-task ...
```