---
title: "Clean Up"
date:
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

#### Overview

To avoid ongoing charges, delete all resources created during this workshop in the reverse order of creation.

{{% notice warning %}}
**Important**: Delete resources in the correct order to avoid dependency errors.
{{% /notice %}}

#### Cleanup Order

Follow this order to delete resources:

#### 1. Delete ECS Services

```bash
# Scale down services
aws ecs update-service --cluster ielts-prod-cluster --service ielts-frontend-service --desired-count 0
aws ecs update-service --cluster ielts-prod-cluster --service ielts-backend-service --desired-count 0

# Wait for tasks to stop
aws ecs wait services-stable --cluster ielts-prod-cluster --services ielts-frontend-service ielts-backend-service

# Delete services
aws ecs delete-service --cluster ielts-prod-cluster --service ielts-frontend-service --force
aws ecs delete-service --cluster ielts-prod-cluster --service ielts-backend-service --force
```

#### 2. Delete ECS Cluster

```bash
aws ecs delete-cluster --cluster ielts-prod-cluster
```

#### 3. Delete Load Balancer

```bash
# Delete listeners first
aws elbv2 delete-listener --listener-arn $LISTENER_ARN

# Delete target groups
aws elbv2 delete-target-group --target-group-arn $FRONTEND_TG_ARN
aws elbv2 delete-target-group --target-group-arn $BACKEND_TG_ARN

# Delete ALB
aws elbv2 delete-load-balancer --load-balancer-arn $ALB_ARN
```

#### 4. Delete Database Resources

```bash
# Delete RDS (skip final snapshot for workshop)
aws rds delete-db-instance --db-instance-identifier ielts-prod-db --skip-final-snapshot

# Delete ElastiCache
aws elasticache delete-cache-cluster --cache-cluster-id ielts-prod-redis
```

#### 5. Delete AI Service Resources

```bash
# Delete Lambda functions
aws lambda delete-function --function-name ielts-writing-evaluate
aws lambda delete-function --function-name ielts-speaking-evaluate
aws lambda delete-function --function-name ielts-flashcard-generate

# Delete SQS queues
aws sqs delete-queue --queue-url https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-writing-queue
aws sqs delete-queue --queue-url https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-speaking-queue
aws sqs delete-queue --queue-url https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-flashcard-queue

# Delete DynamoDB tables
aws dynamodb delete-table --table-name ielts-writing-assessments
aws dynamodb delete-table --table-name ielts-speaking-assessments
aws dynamodb delete-table --table-name ielts-generated-flashcards

# Delete API Gateway
aws apigateway delete-rest-api --rest-api-id $API_ID
```

#### 6. Delete Storage

```bash
# Empty and delete S3 bucket
aws s3 rm s3://ielts-prod-media-{account} --recursive
aws s3 rb s3://ielts-prod-media-{account}

# Delete CloudFront distribution (disable first)
aws cloudfront update-distribution --id $DIST_ID --if-match $ETAG --enabled false
# Wait for distribution to be disabled
aws cloudfront delete-distribution --id $DIST_ID --if-match $ETAG
```

#### 7. Delete ECR Repositories

```bash
aws ecr delete-repository --repository-name ielts-frontend --force
aws ecr delete-repository --repository-name ielts-backend --force
```

#### 8. Delete CI/CD Resources

```bash
aws codepipeline delete-pipeline --name ielts-deployment-pipeline
aws codebuild delete-project --name ielts-backend-build
```

#### 9. Delete Security Resources

```bash
# Delete WAF Web ACL
aws wafv2 delete-web-acl --name ielts-prod-waf --scope REGIONAL --id $WAF_ID --lock-token $LOCK_TOKEN

# Delete secrets
aws secretsmanager delete-secret --secret-id ielts-prod/db --force-delete-without-recovery
aws secretsmanager delete-secret --secret-id ielts-prod/api-keys --force-delete-without-recovery
```

#### 10. Delete VPC Resources

```bash
# Delete NAT Gateways
aws ec2 delete-nat-gateway --nat-gateway-id $NAT_1A
aws ec2 delete-nat-gateway --nat-gateway-id $NAT_1B

# Release Elastic IPs
aws ec2 release-address --allocation-id $EIP_1
aws ec2 release-address --allocation-id $EIP_2

# Delete security groups (in dependency order)
aws ec2 delete-security-group --group-id $REDIS_SG
aws ec2 delete-security-group --group-id $RDS_SG
aws ec2 delete-security-group --group-id $BE_SG
aws ec2 delete-security-group --group-id $FE_SG
aws ec2 delete-security-group --group-id $ALB_SG

# Delete VPC (will delete subnets, route tables, IGW)
aws ec2 delete-vpc --vpc-id $VPC_ID
```

#### 11. Delete CloudWatch Resources

```bash
# Delete log groups
aws logs delete-log-group --log-group-name /ecs/ielts-frontend
aws logs delete-log-group --log-group-name /ecs/ielts-backend

# Delete alarms
aws cloudwatch delete-alarms --alarm-names ielts-backend-high-cpu ielts-alb-5xx-errors ielts-lambda-errors
```

#### 12. Delete IAM Resources

```bash
# Detach policies and delete roles
aws iam detach-role-policy --role-name ecsTaskExecutionRole --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
aws iam delete-role --role-name ecsTaskExecutionRole
aws iam delete-role --role-name ielts-backend-task-role
aws iam delete-role --role-name ielts-lambda-role
```

#### Verify Cleanup

```bash
# Check for remaining resources
aws resourcegroupstaggingapi get-resources --tag-filters Key=Project,Values=ielts
```

#### Cost Summary

After cleanup, verify no charges are accruing:
1. Check **AWS Billing Console** → **Bills**
2. Review **Cost Explorer** for any remaining charges
3. Set up billing alerts for future monitoring

{{% notice tip %}}
Use AWS Resource Groups or tags to easily identify and delete all workshop resources.
{{% /notice %}}

#### Congratulations!

You have successfully completed the IELTS Self-Learning Web System AWS Infrastructure Workshop!

**What you learned:**
- VPC and network design for multi-tier applications
- Container orchestration with ECS Fargate
- Serverless AI architecture with Lambda and Bedrock
- CI/CD pipeline with CodePipeline
- Security best practices with IAM, Secrets Manager, WAF
- Monitoring and observability with CloudWatch

**Next Steps:**
- Deploy your actual application code
- Implement additional features
- Set up production monitoring and alerting
- Configure auto-scaling for production traffic

