---
title: Penetration test a dotnet core Web API with OWASP ZAP
author: Peter Gregg
description: Penetration test a dotnet core Web API with OWASP ZAP
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

# Penetration test a dotnet core Web API with OWASP ZAP

In this article, you will learn how to penetration test a dotnet core Web API project with OWASP ZAP. 

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community with the ASP.NET and web development workload installed. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- A dotnet core Web API project. If you don't have a project you can [follow the first step in these instructions to create one](https://www.pgdevopstips.co.uk/article/structured-logging-with-serilog-and-seq-and-event-viewing-with-elasticsearch-logstash-grafana-and-opserver-in-docker) or [pull down a dotnet core Web API project](https://github.com/petergregg/MonitoringDockerStack).


## Create an Open api definition

1. Create a new folder name **deploy** in the root of the `MonitoringDockerStack` solution.

2. Create a new folder name **zap** in the **deploy** folder.

3. Add a new json file named `monitoredapidefinition` with the following contents.

    ```
    {
    "openapi": "3.0.1",
    "info": {
        "title": "Monitored.API",
        "version": "1.0"
    },
    "servers": [
        {
        "url" : "https://localhost:5021"
        }
    ],
    "paths": {
        "/WeatherForecast": {
        "get": {
            "tags": [
            "WeatherForecast"
            ],
            "operationId": "GetWeatherForecast",
            "parameters": [
            {
                "name": "take",
                "in": "query",
                "schema": {
                "type": "integer",
                "format": "int32",
                "default": 10
                }
            },
            {
                "name": "skip",
                "in": "query",
                "schema": {
                "type": "integer",
                "format": "int32",
                "default": 0
                }
            }
            ],
            "responses": {
            "200": {
                "description": "Success"
            }
            }
        }
        }
    },
    "components": { }
    }
    ```

## Run ZAP API Scan in Docker via Powershell

1. Open Powershell, run the following command to navigate into the zap folder.

    ```
    cd deploy/zap
    ```

2. Execute the following command.

    ```
    docker run -i -t --network=host --rm -v "$(pwd):/zap/wrk/:rw" -t owasp/zap2docker-weekly
    ```

    ![OWASP Zap Run Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/OWASPZAP/OWASPZAPRunDockerContainer.png)


3. Once in the container, run the zap api scan with the following command.

    ```
    ./zap-api-scan.py -g api-scan.conf -t monitoredapidefinition.json -f openapi -r api-scan-report.html
    ```

4. Run the following command to get the name of the running zap container and make a copy of the container name. 

    ```
    docker ps
    ```

5. Run the following command to copy the container files to a folder on your device. Replace `
{ZapContainerName}` with your zap container and `{FolderOnYourDevice}` to a folder on your device.

    ```
    docker cp {ZapContainerName}:/zap/wrk/ "C:\{FolderOnYourDevice}\zap"
    ```

6. Navigate to the copied folder on your device and open `api-scan-report.html` to view the report.

    ![OWASP Zap API Scan Report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/OWASPZAP/OWASPZapAPIScanReport.png)

