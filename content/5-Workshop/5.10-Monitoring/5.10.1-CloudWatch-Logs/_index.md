---
title: "CloudWatch Logs"
date:
weight: 1
chapter: false
pre: " <b> 5.10.1. </b> "
---

#### Overview

Configure CloudWatch Logs for centralized logging.

#### Log Groups

| Log Group | Source | Retention |
|-----------|--------|-----------|
| `/ecs/ielts-frontend` | Frontend containers | 30 days |
| `/ecs/ielts-backend` | Backend containers | 30 days |
| `/aws/lambda/ielts-writing-evaluate` | Lambda functions | 14 days |
| `/aws/lambda/ielts-speaking-evaluate` | Lambda functions | 14 days |
| `/aws/lambda/ielts-flashcard-generate` | Lambda functions | 14 days |

#### Create Log Groups

```bash
# Create log groups with retention
aws logs create-log-group --log-group-name /ecs/ielts-frontend
aws logs put-retention-policy --log-group-name /ecs/ielts-frontend --retention-in-days 30

aws logs create-log-group --log-group-name /ecs/ielts-backend
aws logs put-retention-policy --log-group-name /ecs/ielts-backend --retention-in-days 30
```


#### Log Insights Queries

**Find errors in backend:**
```sql
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 100
```

**API response times:**
```sql
fields @timestamp, @message
| filter @message like /Response time/
| parse @message "Response time: * ms" as response_time
| stats avg(response_time), max(response_time) by bin(5m)
```

#### Next Steps

Proceed to [CloudWatch Alarms](../5.10.2-CloudWatch-Alarms/).

