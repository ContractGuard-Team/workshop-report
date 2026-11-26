---
title: "Workshop"
date: 2026-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Deploying a multi-agent AI contract risk analysis system on AWS

#### Overview

This workshop deploys **ContractGuard AI** from scratch in one AWS account: build a secure VPC, create a
least-privilege IAM role, create SQS queues, S3 buckets, DynamoDB tables and a Secrets Manager secret, then run
9 Docker containers and the legal data pipeline on Amazon EC2. Finally you submit a test contract, follow the three
agents on Langfuse and clean up every resource.

You will practise:
+ **Network and security:** VPC, subnet, a Security Group with no inbound port, VPC Gateway Endpoints for S3 and
  DynamoDB, an IAM instance role with IMDSv2.
+ **Event-driven architecture:** SQS with a Dead-Letter Queue, asynchronous processing, a DynamoDB lease that prevents
  duplicate processing.
+ **Secret management:** API keys live in Secrets Manager and the host's `.env`, never hard-coded in code or GitHub.
+ **Monitoring:** per-stage traces and tokens on Langfuse, metrics/alerts from the monitoring service.

#### Contents

1. [Introduction](5.1-workshop-overview/)
2. [Prerequisites](5.2-prerequisite/)
3. [Network and security](5.3-network-security/)
4. [Data services and secrets](5.4-data-services/)
5. [Deploying to Amazon EC2](5.5-deploy-ec2/)
6. [Legal data pipeline](5.6-data-pipeline/)
7. [Testing and monitoring](5.7-testing/)
8. [Product demo](5.8-demo/)
9. [Troubleshooting and lessons learned](5.9-troubleshooting/)
10. [Clean-up](5.10-cleanup/)
