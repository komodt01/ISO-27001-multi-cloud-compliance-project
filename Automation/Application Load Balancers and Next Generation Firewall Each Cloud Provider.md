# Multi-Cloud Next-Gen Firewall + Load Balancing (AWS, Azure, GCP)

This document shows how to front Next-Gen Firewalls (NGFW) with load balancers across AWS, Azure, and Google Cloud.  
It assumes you already have VPC/VNet/VPC Network and basic security tooling in place.

---

## 🟧 AWS – Network Load Balancer + NGFW Instances

### 1. Create a Network Load Balancer

```bash
aws elbv2 create-load-balancer \
  --name iso27001-nlb \
  --type network \
  --subnets subnet-xxxxxxxx subnet-yyyyyyyy \
  --tags Key=Purpose,Value=ISO27001Security

2. Create a Target Group
