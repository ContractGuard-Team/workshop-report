---
title: "Deploying to Amazon EC2"
date: 2026-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Deploying to Amazon EC2

One EC2 instance runs the whole AI side: 9 Docker Compose containers sharing the `backend` network, plus the
2 systemd services of the data pipeline (part 5.6). The host opens no port; you reach it with Session Manager.

#### Contents

- [Launch EC2 with IMDSv2 and the instance role](5.5.1-launch-ec2/)
- [Install and configure](5.5.2-configure/)
- [Build and run the 9 services](5.5.3-run-services/)
