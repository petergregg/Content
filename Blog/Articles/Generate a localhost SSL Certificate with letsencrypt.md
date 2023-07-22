---
title: Generate a localhost SSL Certificate with letsencrypt
author: Peter Gregg
description: Generate a localhost SSL Certificate with letsencrypt
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2023/07/22 17:10:00
categories: 
  - letsencrypt
  - openssl
  - localhost
  - SSL 
  - Certificate
---

# Generate a localhost SSL Certificate with letsencrypt

In this article, you will learn how to generate a localhost SSL Certificate with letsencrypt. 

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Git for windows installed. If you don't have Git for windows installed, [download Git for windows for free](https://git-scm.com/download/win) before you begin.

# Generate a localhost SSL Certificate with letsencrypt

1. Create a folder named **certificates**.

2. Add a file named `req.cnf` into the **certificates** folder with the following contents. Replace `{YourLocation}`, and `{YourOrganisation}` with your own values.

    ```
    [req]
    distinguished_name = req_distinguished_name
    x509_extensions = v3_req
    prompt = no
    [req_distinguished_name]
    C = GB
    ST = {YourLocation}
    L = {YourLocation}
    O = {YourOrganisation}
    CN = localhost
    [v3_req]
    keyUsage = critical, digitalSignature, keyAgreement
    extendedKeyUsage = serverAuth
    subjectAltName = @alt_names
    [alt_names]
    DNS.1 = localhost
    ```

3. Right click in the **certificates** folder, and select **Git Bash Here**.

    ![GitBash Here](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Git/GitBash/GitBashHere.png)

4. Run the following command to generate an SSL certificate and key in the certificates folder.

    ```
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout localhost.key -out localhost.key -out localhost.crt -config req.cnf -sha256
    ```