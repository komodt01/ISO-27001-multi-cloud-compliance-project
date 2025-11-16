# AWS Security Groups

This module provides ISO 27001-aligned examples for configuring secure network boundaries in AWS using Security Groups.

---

## 1. Create a Security Group for your VPC

~~~bash
aws ec2 create-security-group \
    --group-name iso27001-security-sg \
    --description "Security group for ISO 27001 implementation" \
    --vpc-id vpc-xxxxxxxx
~~~

---

## 2. Add Inbound Rule for SSH Access

~~~bash
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol tcp \
    --port 22 \
    --cidr 10.0.0.0/16
~~~

---

## 3. Add Inbound Rule for HTTPS Access

~~~bash
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol tcp \
    --port 443 \
    --cidr 10.0.0.0/16
~~~

---

## 4. Add Outbound Rule (All Traffic Allowed by Default)

~~~bash
aws ec2 authorize-security-group-egress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol all \
    --port all \
    --cidr 0.0.0.0/0
~~~
