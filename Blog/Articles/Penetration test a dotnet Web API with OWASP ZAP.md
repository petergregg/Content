---
title: Penetration test a dotnet Web API with OWASP ZAP
author: Peter Gregg
description: Penetration test a dotnet Web API with OWASP ZAP
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/07/10 20:00:00
categories: 
  - OWASP ZAP
  - Docker
  - MSSQL
  - Entity Framework core
  - dotnet core
  - Web API
---

# Penetration test a dotnet Web API with OWASP ZAP

In this article, you will learn how to penetration test a dotnet core [Web API project](https://github.com/petergregg/MonitoringDockerStack) with OWASP ZAP. 

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community with the ASP.NET and web development workload installed. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- A dotnet core Web API project. If you don't have a project you can [follow the first step in these instructions to create one](https://www.pgdevopstips.co.uk/article/structured-logging-with-serilog-and-seq-and-event-viewing-with-elasticsearch-logstash-grafana-and-opserver-in-docker).


## Run ZAP API Scan in Docker via Powershell

1. Open Powershell, and execute the following command.

    ```
    docker run -i -t --network=host --rm -v "$(pwd):/zap/wrk/:rw" -t owasp/zap2docker-weekly
    ```

2. Once in the container, run the zap api scan with the following command.

    ```
    ./zap-api-scan.py -g api-scan.conf -t monitoredapidefinition.json -f openapi -r api-scan-report.html
    ```

3. Run the following command to get the name of the running zap container and make a copy of the container name. 

    ```
    docker ps
    ```

4. Run the following command to copy the container files to a folder on your device. Replace `{ZapContainerName}` with your zap container and `{FolderOnYourDevice}` to a folder on your device.

    ```
    docker cp {ZapContainerName}:/zap/wrk/ "C:\{FolderOnYourDevice}\zap"
    ```