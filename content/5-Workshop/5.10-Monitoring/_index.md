---
title: "Monitoring & Logging"
date:
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

#### Overview

Set up Amazon CloudWatch for monitoring, logging, and alerting.

#### Content

1. [CloudWatch Logs](5.10.1-CloudWatch-Logs/)
2. [CloudWatch Alarms](5.10.2-CloudWatch-Alarms/)

#### Monitoring Architecture

| Component | Metrics |
|-----------|---------|
| **ECS** | CPU, Memory, Task Count |
| **RDS** | CPU, Connections, IOPS |
| **ALB** | Request Count, Latency, 5xx Errors |
| **Lambda** | Invocations, Duration, Errors |
| **SQS** | Queue Depth, Message Age |

#### Estimated Time: ~20 minutes

