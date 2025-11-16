# Install Google Cloud CLI

This document provides the steps to install and initialize the Google Cloud CLI on Linux.

---

## 1. Download and Install the Google Cloud CLI

~~~bash
curl https://sdk.cloud.google.com | bash
~~~

---

## 2. Reload Your Shell

~~~bash
exec -l $SHELL
~~~

---

## 3. Initialize gcloud

~~~bash
gcloud init
~~~
