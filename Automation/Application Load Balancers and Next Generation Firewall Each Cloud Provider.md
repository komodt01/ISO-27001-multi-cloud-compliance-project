NultiCloud

# 🟧 AWS — Network Load Balancer + NGFW EC2 Instances

## 1. Create a Network Load Balancer
~~~bash
aws elbv2 create-load-balancer \
  --name iso27001-nlb \
  --type network \
  --subnets subnet-xxxxxxxx subnet-yyyyyyyy \
  --tags Key=Purpose,Value=ISO27001Security
~~~

## 2. Create a Target Group
~~~bash
aws elbv2 create-target-group \
  --name iso27001-targets \
  --protocol TCP \
  --port 443 \
  --vpc-id vpc-xxxxxxxx \
  --health-check-protocol TCP \
  --health-check-port 443
~~~

## 3. Register NGFW Instances
~~~bash
aws elbv2 register-targets \
  --target-group-arn arn:aws:elasticloadbalancing:region:account-id:targetgroup/iso27001-targets/xxxxxxxx \
  --targets Id=i-ngfw1xxxxxxxxx Id=i-ngfw2xxxxxxxxx
~~~

## 4. Create an NLB Listener
~~~bash
aws elbv2 create-listener \
  --load-balancer-arn arn:aws:elasticloadbalancing:region:account-id:loadbalancer/net/iso27001-nlb/xxxxxxxx \
  --protocol TCP \
  --port 443 \
  --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:region:account-id:targetgroup/iso27001-targets/xxxxxxxx
~~~

## 5. Launch NGFW Instances (Example: Palo Alto)
NGFW #1:
~~~bash
aws ec2 run-instances \
  --image-id ami-ngfwxxxxxxx \
  --count 1 \
  --instance-type c5.xlarge \
  --key-name security-key-pair \
  --security-group-ids sg-firewallxxxxxxx \
  --subnet-id subnet-xxxxxxxx \
  --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=NGFW-1}]"
~~~

NGFW #2:
~~~bash
aws ec2 run-instances \
  --image-id ami-ngfwxxxxxxx \
  --count 1 \
  --instance-type c5.xlarge \
  --key-name security-key-pair \
  --security-group-ids sg-firewallxxxxxxx \
  --subnet-id subnet-yyyyyyyy \
  --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=NGFW-2}]"
~~~

---

# 🟦 Azure — Standard Load Balancer + Azure Firewall / NGFW

## 1. Create a Load Balancer
~~~bash
az network lb create \
  --resource-group security-tools-rg \
  --name iso27001-lb \
  --sku Standard \
  --public-ip-address iso27001-lb-pip \
  --frontend-ip-name frontendIP \
  --backend-pool-name backendPool
~~~

## 2. Create a Health Probe
~~~bash
az network lb probe create \
  --resource-group security-tools-rg \
  --lb-name iso27001-lb \
  --name healthProbe \
  --protocol tcp \
  --port 443
~~~

## 3. Create a Load Balancing Rule
~~~bash
az network lb rule create \
  --resource-group security-tools-rg \
  --lb-name iso27001-lb \
  --name httpsRule \
  --protocol tcp \
  --frontend-port 443 \
  --backend-port 443 \
  --frontend-ip-name frontendIP \
  --backend-pool-name backendPool \
  --probe-name healthProbe
~~~

## 4. Deploy Azure Firewalls (Example NGFW)
~~~bash
az network firewall create \
  --name ngfw1 \
  --resource-group security-tools-rg \
  --location eastus

az network firewall create \
  --name ngfw2 \
  --resource-group security-tools-rg \
  --location eastus
~~~

## 5. Create IP Configurations
~~~bash
az network firewall ip-config create \
  --firewall-name ngfw1 \
  --name fw-config \
  --public-ip-address ngfw1-pip \
  --resource-group security-tools-rg \
  --vnet-name security-vnet

az network firewall ip-config create \
  --firewall-name ngfw2 \
  --name fw-config \
  --public-ip-address ngfw2-pip \
  --resource-group security-tools-rg \
  --vnet-name security-vnet
~~~

## 6. Add Firewalls to Backend Pool
~~~bash
az network nic ip-config address-pool add \
  --address-pool backendPool \
  --ip-config-name fw-config \
  --nic-name ngfw1VMNic \
  --resource-group security-tools-rg \
  --lb-name iso27001-lb

az network nic ip-config address-pool add \
  --address-pool backendPool \
  --ip-config-name fw-config \
  --nic-name ngfw2VMNic \
  --resource-group security-tools-rg \
  --lb-name iso27001-lb
~~~

---

# 🟨 Google Cloud — HTTPS Global Load Balancer + NGFW MIG

## 1. Allow Health Checks
~~~bash
gcloud compute firewall-rules create allow-health-checks \
  --network security-network \
  --action allow \
  --direction ingress \
  --rules tcp:443 \
  --source-ranges 35.191.0.0/16,130.211.0.0/22 \
  --target-tags ngfw-nodes
~~~

## 2. Create Instance Template (NGFW Example)
~~~bash
gcloud compute instance-templates create ngfw-template \
  --machine-type n2-standard-4 \
  --network security-network \
  --subnet security-subnet \
  --tags ngfw-nodes \
  --image-family paloaltonetworks-vmseries-flex \
  --image-project paloaltonetworks-public \
  --boot-disk-size 60GB \
  --boot-disk-type pd-ssd
~~~

## 3. Create Managed Instance Group
~~~bash
gcloud compute instance-groups managed create ngfw-mig \
  --template ngfw-template \
  --size 2 \
  --zone us-central1-a
~~~

## 4. Create Health Check
~~~bash
gcloud compute health-checks create tcp ngfw-health-check \
  --port 443 \
  --check-interval 5s \
  --timeout 5s \
  --healthy-threshold 2 \
  --unhealthy-threshold 2
~~~

## 5. Create Backend Service
~~~bash
gcloud compute backend-services create ngfw-backend \
  --protocol HTTPS \
  --health-checks ngfw-health-check \
  --global
~~~

## 6. Add MIG to Backend Service
~~~bash
gcloud compute backend-services add-backend ngfw-backend \
  --instance-group ngfw-mig \
  --instance-group-zone us-central1-a \
  --global
~~~

## 7. Create URL Map
~~~bash
gcloud compute url-maps create ngfw-url-map \
  --default-service ngfw-backend
~~~

## 8. Create HTTPS Proxy + Certificate
~~~bash
gcloud compute ssl-certificates create ngfw-cert \
  --domains example.com

gcloud compute target-https-proxies create ngfw-https-proxy \
  --url-map ngfw-url-map \
  --ssl-certificates ngfw-cert
~~~

## 9. Create Global Forwarding Rule
~~~bash
gcloud compute forwarding-rules create ngfw-forwarding-rule \
  --load-balancing-scheme EXTERNAL \
  --network-tier PREMIUM \
  --address ngfw-ip \
  --global \
  --target-https-proxy ngfw-https-proxy \
  --ports 443
~~~

