---
title: "CloudWatch Alarms"
date:
weight: 2
chapter: false
pre: " <b> 5.10.2. </b> "
---

#### Tổng Quan

Tạo CloudWatch alarms cho critical metrics.

#### Tạo Alarms

**ECS CPU Alarm:**

```bash
aws cloudwatch put-metric-alarm \
    --alarm-name ielts-backend-high-cpu \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 2 \
    --metric-name CPUUtilization \
    --namespace AWS/ECS \
    --period 300 \
    --statistic Average \
    --threshold 80 \
    --alarm-actions arn:aws:sns:ap-southeast-1:{account}:ielts-alerts \
    --dimensions Name=ServiceName,Value=ielts-backend-service Name=ClusterName,Value=ielts-prod-cluster
```

**ALB 5xx Errors Alarm:**

```bash
aws cloudwatch put-metric-alarm \
    --alarm-name ielts-alb-5xx-errors \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 1 \
    --metric-name HTTPCode_Target_5XX_Count \
    --namespace AWS/ApplicationELB \
    --period 60 \
    --statistic Sum \
    --threshold 10 \
    --alarm-actions arn:aws:sns:ap-southeast-1:{account}:ielts-alerts
```

**Lambda Errors Alarm:**

```bash
aws cloudwatch put-metric-alarm \
    --alarm-name ielts-lambda-errors \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 1 \
    --metric-name Errors \
    --namespace AWS/Lambda \
    --period 300 \
    --statistic Sum \
    --threshold 5 \
    --alarm-actions arn:aws:sns:ap-southeast-1:{account}:ielts-alerts \
    --dimensions Name=FunctionName,Value=ielts-writing-evaluate
```


#### SNS Topic cho Alerts

```bash
# Tạo SNS topic
aws sns create-topic --name ielts-alerts

# Subscribe email
aws sns subscribe \
    --topic-arn arn:aws:sns:ap-southeast-1:{account}:ielts-alerts \
    --protocol email \
    --notification-endpoint your-email@example.com
```

#### Bước Tiếp Theo

Tiến hành đến [Clean Up](../../5.11-Cleanup/) để xem hướng dẫn dọn dẹp resources.

