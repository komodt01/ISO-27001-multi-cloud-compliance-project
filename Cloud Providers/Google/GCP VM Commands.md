# GCP Security Monitoring Network & VM Setup

This module sets up a secure VPC, subnet, firewall rules, monitoring VM, and persistent disk for centralized log collection—aligned with ISO 27001 controls for monitoring, logging, and infrastructure security.

Replace:
- `PROJECT-ID`  
- Any region/zone names if needed  

---

## 1. Create a VPC Network

~~~bash
gcloud compute networks create security-network \
    --project PROJECT-ID \
    --subnet-mode=auto
~~~

---

## 2. Create a Firewall Rule for SSH Access

~~~bash
gcloud compute firewall-rules create allow-ssh \
    --project PROJECT-ID \
    --network security-network \
    --allow tcp:22 \
    --source-ranges 35.235.240.0/20
~~~

*(35.235.240.0/20 is the recommended range for IAP / secure SSH tunneling.)*

---

## 3. Create a Subnet for Security Tools

~~~bash
gcloud compute networks subnets create security-subnet \
    --project PROJECT-ID \
    --network security-network \
    --region us-central1 \
    --range 10.0.0.0/24
~~~

---

## 4. Create a Security Monitoring Server VM

~~~bash
gcloud compute instances create security-monitoring-server \
    --project PROJECT-ID \
    --zone us-central1-a \
    --machine-type e2-standard-2 \
    --subnet security-subnet \
    --network-tier PREMIUM \
    --maintenance-policy MIGRATE \
    --image-family ubuntu-2004-lts \
    --image-project ubuntu-os-cloud \
    --boot-disk-size 100GB \
    --boot-disk-type pd-balanced \
    --boot-disk-device-name security-monitoring-server \
    --tags security,monitoring \
    --metadata purpose=iso27001
~~~

---

## 5. Create a Persistent Disk for Log Storage

~~~bash
gcloud compute disks create security-logs-disk \
    --project PROJECT-ID \
    --size 1TB \
    --zone us-central1-a \
    --type pd-standard
~~~

---

## 6. Attach the Disk to the Monitoring VM

~~~bash
gcloud compute instances attach-disk security-monitoring-server \
    --project PROJECT-ID \
    --disk security-logs-disk \
    --zone us-central1-a
~~~

---

## 7. Format & Mount the Disk Over SSH

⚠️ *This command MUST be run as one quoted SSH call.*

~~~bash
gcloud compute ssh security-monitoring-server \
    --project PROJECT-ID \
    --zone us-central1-a \
    --command "
        sudo mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdc &&
        sudo mkdir -p /mnt/disks/logs &&
        sudo mount -o discard,defaults /dev/sdc /mnt/disks/logs &&
        sudo chmod a+w /mnt/disks/logs &&
        echo UUID=\$(sudo blkid -s UUID -o value /dev/sdc) /mnt/disks/logs ext4 discard,defaults,nofail 0 2 | sudo tee -a /etc/fstab
    "
~~~
