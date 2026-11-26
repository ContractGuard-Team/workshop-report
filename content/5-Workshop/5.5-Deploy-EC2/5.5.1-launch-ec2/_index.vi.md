---
title: "Khởi tạo EC2"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

Cấu hình đề xuất: Ubuntu 24.04, **t3.xlarge** (4 vCPU, 16 GiB RAM: model embedding và reranker chạy CPU, mỗi tiến trình
nạp model khoảng 2,2 GB), ổ gp3 60 GB mã hóa (image + build cache + ~3,3 GB model). Chọn loại máy khác theo dự toán
chi phí của bạn nếu cần.

{{% notice warning %}}
`HttpPutResponseHopLimit=2` là bắt buộc. Với giá trị 1, máy host lấy được credential nhưng container Docker thì không
(`NoCredentialsError`), vì request IMDS từ container phải đi qua thêm một hop của bridge network.
{{% /notice %}}

1. Khởi tạo instance từ CloudShell, không gắn key pair:


```bash
source ~/cr.env
AMI_ID=$(aws ssm get-parameter \
  --name /aws/service/canonical/ubuntu/server/24.04/stable/current/amd64/hvm/ebs-gp3/ami-id \
  --query Parameter.Value --output text)
INSTANCE_ID=$(aws ec2 run-instances --image-id "$AMI_ID" --instance-type t3.xlarge \
  --subnet-id "$SUBNET_ID" --security-group-ids "$SG_ID" \
  --iam-instance-profile Name=ContractRisk-EC2-Profile \
  --metadata-options HttpEndpoint=enabled,HttpTokens=required,HttpPutResponseHopLimit=2 \
  --block-device-mappings '[{"DeviceName":"/dev/sda1","Ebs":{"VolumeSize":60,"VolumeType":"gp3","Encrypted":true}}]' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=contract-risk-ec2}]' \
  --query 'Instances[0].InstanceId' --output text)
echo "export INSTANCE_ID=$INSTANCE_ID" >> ~/cr.env
aws ec2 wait instance-status-ok --instance-ids "$INSTANCE_ID"
aws ec2 describe-instances --instance-ids "$INSTANCE_ID" \
  --query 'Reservations[0].Instances[0].[State.Name,MetadataOptions.HttpTokens,MetadataOptions.HttpPutResponseHopLimit]'
```

{{% notice tip %}}
📸 **Ảnh cần chụp:** EC2 console → **Instances** → `contract-risk-ec2`: *Instance state* = Running, *Status check* = 2/2, tab **Security** thấy IAM role `ContractRisk-EC2-Role` và Security Group `contract-risk-ec2-sg`.
{{% /notice %}}

![EC2 instance](/images/5-Workshop/5.5-Deploy-EC2/ec2-instance.png)

{{% notice tip %}}
📸 **Ảnh cần chụp:** Chọn instance → **Actions → Instance settings → Modify instance metadata options**: *IMDSv2* = Required, *Metadata response hop limit* = 2 (chụp rồi bấm Cancel).
{{% /notice %}}

![IMDSv2 và hop limit](/images/5-Workshop/5.5-Deploy-EC2/imds-settings.png)


2. Vào máy bằng Session Manager: EC2 console → chọn `contract-risk-ec2` → **Connect** → **Session Manager** →
   **Connect**, rồi chuyển sang user `ubuntu`:

```bash
sudo su - ubuntu
```


{{% notice tip %}}
📸 **Ảnh cần chụp:** EC2 console → chọn instance → **Connect** → tab **Session Manager** → **Connect**: terminal trên trình duyệt sau khi chạy `sudo su - ubuntu` và `whoami`.
{{% /notice %}}

![Kết nối Session Manager](/images/5-Workshop/5.5-Deploy-EC2/session-manager.png)
