---
title: Structured logging with Serilog and Seq and Event Viewing with Elasticsearch Logstash Grafana and Opserver in Docker
author: Peter Gregg
description: Structured logging with Serilog and Seq and Event Viewing with Elasticsearch Logstash Grafana and Opserver in Docker
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2022/01/21 13:00:00
categories: 
  - Serilog
  - Seq
  - Elasticsearch
  - Logstash
  - Grafana
  - Opserver
  - Docker
  - MSSQL
  - EF Core
  - dotnet
---

# Structured logging with Serilog and Seq and Event Viewing with Elasticsearch Logstash Grafana and Opserver in Docker

In this article, you will learn how to add structured logging with Serilog, Seq and event viewing with Elasticsearch, Logstash, Grafana, and Opserver under Docker containers. 

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.

## Create a Web API Project in Visual Studio with Docker Support

1. Open **Visual Studio**
2. Select **Create a new project**.
    ![Visual Studio Create New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateNewProject.png)
3. Search for **core web api**, and then select **ASP.NET Core Web API**, and then select **Next**.

    ![Visual Studio Create New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateDotNetCoreWebAPIProject.png)

4. Enter the following values in **Configure your new project window**, and then select **Next**
    ![Visual Studio Configure New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureDotNetCoreWebAPIProject.png)

5. Enter the following values in **Additional information**, and then select **Create**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureDockerDotNetCoreWebAPIProject.png)

# Install the following nuget packages for Serilog, Seq, Elastic Search
1. In Visual Studio, open the Package Manager Console and enter:

    Install-Package Serilog.AspNetCore
    Install-Package Serilog.Sinks.Seq
    Install-Package Serilog.Sinks.ElasticSearch
    Install-Package Elastic.Apm.SerilogEnricher

## Enable Serilog in program.cs

1. Add the following code to the program.cs and resolve any usings under `var builder = WebApplication.CreateBuilder(args);`.

    ```
        var builder = WebApplication.CreateBuilder(args);

        builder.Host.UseSerilog((context, logger) => logger
       .WriteTo.Console()
       .WriteTo.Elasticsearch(new ElasticsearchSinkOptions(new Uri(context.Configuration["ElasticSearch:ServerUrl"]))
       {
           AutoRegisterTemplate = true,
           AutoRegisterTemplateVersion = AutoRegisterTemplateVersion.ESv7,
           FailureCallback = e => Console.WriteLine($"Unable to submit event to Elasticsearch {e.MessageTemplate}"),
       })
       .Enrich.WithElasticApmCorrelationInfo()
       .ReadFrom.Configuration(context.Configuration));
    ```

## Add AppSettings to Web API project

1. Add the following json into the **appsettings.json** and **appsettings.Development.json** files.

    ```
    {
    "Serilog": {
        "Properties": {
        "Application": "MonitoredAPI"
        },
        "WriteTo": [
        {
            "Name": "Seq",
            "Args": {
            "serverUrl": "http://seq",
            "apiKey": "<secret>"
            }
        }
        ],
        "MinimumLevel": {
        "Default": "Debug",
        "Override": {
            "Microsoft": "Debug",
            "Monitored.API": "Debug",
            "System" : "Warning",
            "Microsoft.Hosting.Lifetime": "Information"
        }
        }
    },
    "ElasticSearch": {
        "ServerUrl": "http://elasticsearch:9200"
    }
    }
    ```

## Add Docker Compose to a Visual Studio Solution

1. Right click on the **Web API project**, and then select **Add** > **Docker Support**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolution.png)

2. Select **Docker Compose**, and then **OK**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolutionOrchestrator.png)

3. Select **Linux**, and then **OK**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolutionTargetOS.png)

4. A **docker-compose** project will be added to the solution.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioDockerComposeProjectInSolution.png)

