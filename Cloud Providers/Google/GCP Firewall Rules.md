# Google Cloud Firewall Rules for Security Monitoring

These commands configure internal SSH and HTTPS access within a secure VPC network using firewall rules and VM tags.

Replace values such as:
- `PROJECT-ID`
- `security-monitoring-server`
- `us-central1-a`
- `security-network`

before running.

---

## 1. Create Firewall Rule for Internal SSH Access (Port 22)

~~~bash
gcloud compute firewall-rules create allow-ssh-internal \
    --project PROJECT-ID \
    --network security-network \
    --action allow \
    --direction ingress \
    --rules tcp:22 \
    --source-ranges 10.0.0.0/16 \
    --priority 1000 \
    --target-tags security-monitoring
~~~

---

## 2. Create Firewall Rule for Internal HTTPS Access (Port 443)

~~~bash
gcloud compute firewall-rules create allow-https-internal \
    --project PROJECT-ID \
    --network security-network \
    --action allow \
    --direction ingress \
    --rules tcp:443 \
    --source-ranges 10.0.0.0/16 \
    --priority 1010 \
    --target-tags security-monitoring
~~~
---

## 3. Add Tag to Security Monitoring VM

This allows the VM to match the `--target-tags security-monitoring` firewall selector.

~~~bash
gcloud compute instances add-tags security-monitoring-server \
    --project PROJECT-ID \
    --tags security-monitoring \
    --zone us-central1-a
~~~
