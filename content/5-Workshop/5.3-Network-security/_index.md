---
title: "Network and security"
date: 2026-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Network and security

This part builds the base for EC2: a VPC with one subnet that reaches the Internet through an Internet Gateway, a
Security Group with **no inbound port**, two VPC Gateway Endpoints so that S3/DynamoDB traffic never crosses the
Internet, and an IAM role granting exactly what the code calls.

#### Contents

- [Create the VPC, subnet and Security Group](5.3.1-create-vpc/)
- [Create VPC Gateway Endpoints for S3 and DynamoDB](5.3.2-gateway-endpoints/)
- [Create a least-privilege IAM role](5.3.3-iam-role/)
