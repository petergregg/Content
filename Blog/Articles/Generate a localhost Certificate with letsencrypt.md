---
title: Generate a localhost Certificate with letsencrypt
author: Peter Gregg
description: Generate a localhost Certificate with letsencrypt
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/07/14 23:30:00
categories: 
  - Letsencrypt
  - localhost
  - Generate Certificate
---

# Generate a localhost Certificate with letsencrypt

1. Create folder named **certificates**.
2. Right click in the **certificates** folder and select **Git Bash Here**.

    ![GitBash Here](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Git/GitBash/GitBashHere.png)


3. Add a file named `req.cnf` into the **certificates** folder.

    ```
    [req]
    distinguished_name = req_distinguished_name
    x509_extensions = v3_req
    prompt = no
    [req_distinguished_name]
    C = GB
    ST = Liverpool
    L = Liverpool
    O = PGDevOpsTips
    CN = www.localhost.com
    [v3_req]
    keyUsage = critical, digitalSignature, keyAgreement
    extendedKeyUsage = serverAuth
    subjectAltName = @alt_names
    [alt_names]
    DNS.1 = www.localhost.com
    DNS.2 = localhost.com
    DNS.3 = localhost
    ```

4. Run the following command to generate a certificate.

    ```
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout localhost.key -out localhost.key -out localhost.crt -config req.cnf -sha256
    ```