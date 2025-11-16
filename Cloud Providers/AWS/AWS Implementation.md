# AWS Implementation

This document provides the basic setup steps for preparing an AWS environment for ISO 27001-aligned activities.

---

## 1. Install AWS CLI

~~~bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
~~~

---

## 2. Configure AWS Credentials

~~~bash
aws configure
~~~
