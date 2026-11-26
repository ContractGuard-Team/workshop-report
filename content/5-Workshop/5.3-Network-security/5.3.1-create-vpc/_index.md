---
title: "Create the VPC, subnet and Security Group"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

Run these in CloudShell.

1. Create the `10.0.0.0/16` VPC and enable DNS hostnames (needed by Session Manager and endpoints):

```bash
source ~/cr.env
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=contract-risk-vpc}]' \
  --query Vpc.VpcId --output text)
aws ec2 modify-vpc-attribute --vpc-id "$VPC_ID" --enable-dns-hostnames '{"Value":true}'
echo "export VPC_ID=$VPC_ID" >> ~/cr.env
```

2. Create the `10.0.1.0/24` subnet in `ap-southeast-1a` with auto-assigned public IPv4, so EC2 reaches the Internet
   without a NAT Gateway:

```bash
SUBNET_ID=$(aws ec2 create-subnet --vpc-id "$VPC_ID" --cidr-block 10.0.1.0/24 \
  --availability-zone ap-southeast-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=contract-risk-subnet-a}]' \
  --query Subnet.SubnetId --output text)
aws ec2 modify-subnet-attribute --subnet-id "$SUBNET_ID" --map-public-ip-on-launch
echo "export SUBNET_ID=$SUBNET_ID" >> ~/cr.env
```

3. Create the Internet Gateway, the route table and the default route:

```bash
IGW_ID=$(aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=contract-risk-igw}]' \
  --query InternetGateway.InternetGatewayId --output text)
aws ec2 attach-internet-gateway --internet-gateway-id "$IGW_ID" --vpc-id "$VPC_ID"
RT_ID=$(aws ec2 create-route-table --vpc-id "$VPC_ID" \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=contract-risk-rt}]' \
  --query RouteTable.RouteTableId --output text)
aws ec2 create-route --route-table-id "$RT_ID" --destination-cidr-block 0.0.0.0/0 --gateway-id "$IGW_ID"
aws ec2 associate-route-table --route-table-id "$RT_ID" --subnet-id "$SUBNET_ID"
echo "export IGW_ID=$IGW_ID RT_ID=$RT_ID" >> ~/cr.env
```


{{% notice tip %}}
📸 **Screenshot to take:** VPC console → **Your VPCs** → select `contract-risk-vpc` → **Resource map** tab: subnet, route table `contract-risk-rt` and the Internet Gateway visible.
{{% /notice %}}

![VPC resource map](/images/5-Workshop/5.3-Network-security/vpc-resource-map.png)


4. Create the Security Group: remove the default allow-all outbound rule and only allow TCP 443 out (AWS APIs,
   Gemini, Qdrant, Supabase, Tavily, Langfuse, GitHub, Docker Hub) and TCP 80 (Ubuntu apt mirrors).
   **Add no inbound rule.**

```bash
SG_ID=$(aws ec2 create-security-group --group-name contract-risk-ec2-sg \
  --description "ContractGuard AI EC2: no inbound, outbound 80/443 only" --vpc-id "$VPC_ID" \
  --query GroupId --output text)
aws ec2 revoke-security-group-egress --group-id "$SG_ID" \
  --ip-permissions '[{"IpProtocol":"-1","IpRanges":[{"CidrIp":"0.0.0.0/0"}]}]'
aws ec2 authorize-security-group-egress --group-id "$SG_ID" --ip-permissions \
  '[{"IpProtocol":"tcp","FromPort":443,"ToPort":443,"IpRanges":[{"CidrIp":"0.0.0.0/0"}]},
    {"IpProtocol":"tcp","FromPort":80,"ToPort":80,"IpRanges":[{"CidrIp":"0.0.0.0/0"}]}]'
echo "export SG_ID=$SG_ID" >> ~/cr.env
```

{{% notice info %}}
The 9 containers talk to each other over the `backend` Docker network inside the host, so no port needs opening. The
monitoring service only binds `127.0.0.1:8099` for the data pipeline on the same host.
{{% /notice %}}


{{% notice tip %}}
📸 **Screenshot to take:** EC2 console → **Security Groups** → `contract-risk-ec2-sg`: the **Inbound rules** tab is empty, the **Outbound rules** tab only has TCP 80 and 443.
{{% /notice %}}

![Security Group with no inbound rule](/images/5-Workshop/5.3-Network-security/security-group.png)
