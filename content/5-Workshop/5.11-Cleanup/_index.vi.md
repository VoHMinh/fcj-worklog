---
title: "Dọn Dẹp"
date:
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

#### Tổng Quan

Để tránh chi phí phát sinh, xóa tất cả resources được tạo trong workshop này theo thứ tự ngược lại.

{{% notice warning %}}
**Quan trọng**: Xóa resources theo đúng thứ tự để tránh dependency errors.
{{% /notice %}}

#### Thứ Tự Cleanup

Theo thứ tự này để xóa resources:

#### 1. Xóa ECS Services

```bash
# Scale down services
aws ecs update-service --cluster ielts-prod-cluster --service ielts-frontend-service --desired-count 0
aws ecs update-service --cluster ielts-prod-cluster --service ielts-backend-service --desired-count 0

# Đợi tasks dừng
aws ecs wait services-stable --cluster ielts-prod-cluster --services ielts-frontend-service ielts-backend-service

# Xóa services
aws ecs delete-service --cluster ielts-prod-cluster --service ielts-frontend-service --force
aws ecs delete-service --cluster ielts-prod-cluster --service ielts-backend-service --force
```

#### 2. Xóa ECS Cluster

```bash
aws ecs delete-cluster --cluster ielts-prod-cluster
```

#### 3. Xóa Load Balancer

```bash
# Xóa listeners trước
aws elbv2 delete-listener --listener-arn $LISTENER_ARN

# Xóa target groups
aws elbv2 delete-target-group --target-group-arn $FRONTEND_TG_ARN
aws elbv2 delete-target-group --target-group-arn $BACKEND_TG_ARN

# Xóa ALB
aws elbv2 delete-load-balancer --load-balancer-arn $ALB_ARN
```

#### 4. Xóa Database Resources

```bash
# Xóa RDS (skip final snapshot cho workshop)
aws rds delete-db-instance --db-instance-identifier ielts-prod-db --skip-final-snapshot

# Xóa ElastiCache
aws elasticache delete-cache-cluster --cache-cluster-id ielts-prod-redis
```

#### 5. Xóa AI Service Resources

```bash
# Xóa Lambda functions
aws lambda delete-function --function-name ielts-writing-evaluate
aws lambda delete-function --function-name ielts-speaking-evaluate
aws lambda delete-function --function-name ielts-flashcard-generate

# Xóa SQS queues
aws sqs delete-queue --queue-url https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-writing-queue
aws sqs delete-queue --queue-url https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-speaking-queue
aws sqs delete-queue --queue-url https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-flashcard-queue

# Xóa DynamoDB tables
aws dynamodb delete-table --table-name ielts-writing-assessments
aws dynamodb delete-table --table-name ielts-speaking-assessments
aws dynamodb delete-table --table-name ielts-generated-flashcards

# Xóa API Gateway
aws apigateway delete-rest-api --rest-api-id $API_ID
```

#### 6. Xóa Storage

```bash
# Empty và xóa S3 bucket
aws s3 rm s3://ielts-prod-media-{account} --recursive
aws s3 rb s3://ielts-prod-media-{account}

# Xóa CloudFront distribution (disable trước)
aws cloudfront update-distribution --id $DIST_ID --if-match $ETAG --enabled false
# Đợi distribution disabled
aws cloudfront delete-distribution --id $DIST_ID --if-match $ETAG
```

#### 7. Xóa ECR Repositories

```bash
aws ecr delete-repository --repository-name ielts-frontend --force
aws ecr delete-repository --repository-name ielts-backend --force
```

#### 8. Xóa CI/CD Resources

```bash
aws codepipeline delete-pipeline --name ielts-deployment-pipeline
aws codebuild delete-project --name ielts-backend-build
```

#### 9. Xóa Security Resources

```bash
# Xóa WAF Web ACL
aws wafv2 delete-web-acl --name ielts-prod-waf --scope REGIONAL --id $WAF_ID --lock-token $LOCK_TOKEN

# Xóa secrets
aws secretsmanager delete-secret --secret-id ielts-prod/db --force-delete-without-recovery
aws secretsmanager delete-secret --secret-id ielts-prod/api-keys --force-delete-without-recovery
```

#### 10. Xóa VPC Resources

```bash
# Xóa NAT Gateways
aws ec2 delete-nat-gateway --nat-gateway-id $NAT_1A
aws ec2 delete-nat-gateway --nat-gateway-id $NAT_1B

# Release Elastic IPs
aws ec2 release-address --allocation-id $EIP_1
aws ec2 release-address --allocation-id $EIP_2

# Xóa security groups (theo thứ tự dependency)
aws ec2 delete-security-group --group-id $REDIS_SG
aws ec2 delete-security-group --group-id $RDS_SG
aws ec2 delete-security-group --group-id $BE_SG
aws ec2 delete-security-group --group-id $FE_SG
aws ec2 delete-security-group --group-id $ALB_SG

# Xóa VPC (sẽ xóa subnets, route tables, IGW)
aws ec2 delete-vpc --vpc-id $VPC_ID
```

#### 11. Xóa CloudWatch Resources

```bash
# Xóa log groups
aws logs delete-log-group --log-group-name /ecs/ielts-frontend
aws logs delete-log-group --log-group-name /ecs/ielts-backend

# Xóa alarms
aws cloudwatch delete-alarms --alarm-names ielts-backend-high-cpu ielts-alb-5xx-errors ielts-lambda-errors
```

#### 12. Xóa IAM Resources

```bash
# Detach policies và xóa roles
aws iam detach-role-policy --role-name ecsTaskExecutionRole --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
aws iam delete-role --role-name ecsTaskExecutionRole
aws iam delete-role --role-name ielts-backend-task-role
aws iam delete-role --role-name ielts-lambda-role
```

#### Xác Minh Cleanup

```bash
# Kiểm tra remaining resources
aws resourcegroupstaggingapi get-resources --tag-filters Key=Project,Values=ielts
```

#### Tóm Tắt Chi Phí

Sau cleanup, xác minh không có chi phí phát sinh:
1. Kiểm tra **AWS Billing Console** → **Bills**
2. Review **Cost Explorer** cho bất kỳ remaining charges
3. Thiết lập billing alerts cho future monitoring

{{% notice tip %}}
Sử dụng AWS Resource Groups hoặc tags để dễ dàng identify và xóa tất cả workshop resources.
{{% /notice %}}

#### Chúc Mừng!

Bạn đã hoàn thành thành công IELTS Self-Learning Web System AWS Infrastructure Workshop!

**Những gì bạn đã học:**
- VPC và network design cho multi-tier applications
- Container orchestration với ECS Fargate
- Serverless AI architecture với Lambda và Bedrock
- CI/CD pipeline với CodePipeline
- Security best practices với IAM, Secrets Manager, WAF
- Monitoring và observability với CloudWatch

**Bước Tiếp Theo:**
- Deploy actual application code của bạn
- Implement additional features
- Thiết lập production monitoring và alerting
- Cấu hình auto-scaling cho production traffic

