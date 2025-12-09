---
title: "Monitoring & Logging"
date:
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

#### Tổng Quan

Thiết lập Amazon CloudWatch cho monitoring, logging, và alerting.

#### Nội Dung

1. [CloudWatch Logs](5.10.1-CloudWatch-Logs/)
2. [CloudWatch Alarms](5.10.2-CloudWatch-Alarms/)

#### Kiến Trúc Monitoring

| Component | Metrics |
|-----------|---------|
| **ECS** | CPU, Memory, Task Count |
| **RDS** | CPU, Connections, IOPS |
| **ALB** | Request Count, Latency, 5xx Errors |
| **Lambda** | Invocations, Duration, Errors |
| **SQS** | Queue Depth, Message Age |

#### Thời Gian Ước Tính: ~20 phút

