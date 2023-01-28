---
title: Structured logging with Serilog and Seq and Event Viewing with Elasticsearch, Logstash, Grafana and Opserver in Docker
author: Peter Gregg
description: Structured logging to a dotnet core Web API project using Serilog and Seq and Event Viewing with Elasticsearch, Logstash, Grafana and Opserver in Docker
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

# Structured logging with Serilog and Seq and Event Viewing with Elasticsearch, Logstash, Grafana and Opserver in Docker

In this article, you will learn how to add Structured logging to a dotnet core Web API project using Serilog and Seq and Event Viewing with Elasticsearch, Logstash, Grafana and Opserver in Docker containers. 

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.

## Create a Web API Project in Visual Studio with Docker Support

1. Open **Visual Studio**
2. Select **Create a new project**..

    ![Visual Studio Create New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateNewProject.png)
3. Search for **core web api**, and then select **ASP.NET Core Web API**, and then select **Next**.

    ![Visual Studio Create New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateDotNetCoreWebAPIProject.png)

4. Enter the following values in **Configure your new project window**, and then select **Next**
    ![Visual Studio Configure New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureDotNetCoreWebAPIProject.png)

5. Enter the following values in **Additional information**, and then select **Create**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureDockerDotNetCoreWebAPIProject.png)

# Install the following nuget packages for Serilog, Seq, Elastic Search and MS SQL support
1. In Visual Studio, open the **Package Manager Console** and enter:

    Install-Package Serilog.AspNetCore

    Install-Package Serilog.Sinks.Seq
    
    Install-Package Serilog.Sinks.ElasticSearch
    
    Install-Package Elastic.Apm.SerilogEnricher
    
    Install Microsoft.EntityFrameworkCore.SqlServer
    
    Install AutoFixture

## Enable Serilog in program.cs

1. Add the following code to the program.cs under `var builder = WebApplication.CreateBuilder(args);` and resolve any usings.

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
## Add SQL Database to Project

## Add Model

1. Add the following Model **WeatherForecast.cs** into the Data folder.

    ```
    public class WeatherForecast
    {
        public int Id { get; set; }

        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);

        public string? Summary { get; set; }
    }
    ```

### Add DB Context

1. Add the following class **MonitoredAPIDataContext.cs** to the **Data** folder with the following content.

    ```
    using Microsoft.EntityFrameworkCore;

    namespace Monitored.API.Data
    {
        public class MonitoredAPIDataContext : DbContext
        {
            public MonitoredAPIDataContext(DbContextOptions<MonitoredAPIDataContext> options) : base(options)
            {
            }

            public DbSet<WeatherForecast> WeatherForecasts { get; set; }
        }
    }
    ```

### Add Seed Data
1. Add the following class **Seeder.cs** into the **Data** folder.

    ```
    using AutoFixture;

    namespace Monitored.API.Data
    {
        public static class Seeder
        {

            // This is purely for a demo, don't anything like this in a real application!
            public static void Seed(this MonitoredAPIDataContext monitoredAPIDataContext)
            {
                if (!monitoredAPIDataContext.WeatherForecasts.Any())
                {
                    Fixture fixture = new Fixture();
                    fixture.Customize<WeatherForecast>(weatherForecast => weatherForecast.Without(p => p.Id));
                    //--- The next two lines add 100 rows to your database
                    List<WeatherForecast> weatherForecasts = fixture.CreateMany<WeatherForecast>(100).ToList();
                    monitoredAPIDataContext.AddRange(weatherForecasts);
                    monitoredAPIDataContext.SaveChanges();
                }
            }
        }
    }
    ```

### Enable Use of SQL
1. Add the following code to the program.cs under the serilog configuration and resolve any usings. 

    ```
    builder.Services.AddDbContext<MonitoredAPIDataContext>(options =>
        options.UseSqlServer(builder.Configuration.GetConnectionString("monitoredapidb")));
    ```
## Replace the code in WeatherController.cs

1. Replace the code in the **WeatherController.cs** with the following code. 

    ```
    private readonly ILogger<WeatherForecastController> _logger;
    private readonly MonitoredAPIDataContext _monitoredAPIDataContext;

    public WeatherForecastController(ILogger<WeatherForecastController> logger, MonitoredAPIDataContext monitoredAPIDataContext)
    {
        _logger = logger;
        _monitoredAPIDataContext = monitoredAPIDataContext;
    }

    [HttpGet(Name = "GetWeatherForecast")]
    public ActionResult Get(int take = 10, int skip = 0)
    {
        _logger.LogInformation("Starting Get request");

        return Ok(_monitoredAPIDataContext.WeatherForecasts.OrderBy(p => p.Id).Skip(skip).Take(take));

    }
    ```
        
## Add AppSettings to Web API project

1. Add the following json into the **appsettings.json** and **appsettings.Development.json** files.

    ```
    {
    "Serilog": {
        "Properties": {
        "Application": "Monitored.API"
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

## Add Docker Compose Project to a Visual Studio Solution

1. Right click on the **Web API project**, and then select **Add** > **Docker Support**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolution.png)

2. Select **Docker Compose**, and then **OK**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolutionOrchestrator.png)

3. Select **Linux**, and then **OK**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolutionTargetOS.png)

4. A **docker-compose** project will be added to the solution.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioDockerComposeProjectInSolution.png)

## Define Container Applications in Docker Compose

1. Add the following code to the **docker-compose.yml** file after the **monitored.api** service block.

    ```
    sqldata:
        image: mcr.microsoft.com/mssql/server:2022-latest

    seq:
        image: datalust/seq:latest

    elasticsearch:
        image: docker.elastic.co/elasticsearch/elasticsearch:7.17.8

    grafana:
        image: grafana/grafana-oss:latest
        depends_on:
        - elasticsearch

    opserver:
        image: opserver/opserver:preview1
    ```
2. Add the following code to the **docker-compose.override.yml** file after the **monitored.api** service block.

    ```
    sqldata:
        container_name: sqldata
        environment:
        - SA_PASSWORD=Pass@word
        - ACCEPT_EULA=Y
        ports:
        - "1433:1433"

    seq:
        container_name: seq
        environment:
        - ACCEPT_EULA=Y
        ports:
        - "5340:80"
        volumes:
        - /data:/data
        restart: always

    elasticsearch:
        container_name: elasticsearch
        restart: always
        ports:
        - 9200:9200
        environment:
        - discovery.type=single-node
        - network.host=0.0.0.0
        - xpack.security.enabled=false
        volumes:
        - ./deploy/elg/elasticsearch:/usr/share/elasticsearch/data:rw
        ulimits:
        memlock:
            soft: -1
            hard: -1

    grafana:
        container_name: grafana
        ports:
        - "3000:3000"
        volumes:
        - ./deploy/elg/grafana/provisioning:/etc/grafana/provisioning:rw
        restart: always

    opserver:
        container_name: opserver
        ports:
        - "4001:80"
        volumes:
        - ./deploy/opserver/config:/app/Config
    ```
## Create Configuration for ElasticSearch and Grafana

### Create ELG folder structure

1. Create the following folder structure. Create **deploy** folder in thr root of the solution with the following folders in **elasticsearch**, **logstash** and **grafana**.


    ![Visual Studio ELG Folder Structure](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioELGFolderStructure.png)

### Create elasticsearch configuration

1. Create a new folder named **config** in the **elasticsearch** folder and add a **elasticsearch.yml** file with the following contents.
    ```
    ## Default Elasticsearch configuration from elasticsearch-docker.
    ## from https://github.com/elastic/elasticsearch-docker/blob/master/build/elasticsearch/elasticsearch.yml
    #
    cluster.name: "docker-cluster"
    network.host: 0.0.0.0

    # minimum_master_nodes need to be explicitly set when bound on a public IP
    # set to 1 to allow single node clusters
    # Details: https://github.com/elastic/elasticsearch/pull/17288
    discovery.zen.minimum_master_nodes: 1

    ## Use single node discovery in order to disable production mode and avoid bootstrap checks
    ## see https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
    #
    discovery.type: single-node
    xpack.security.enabled: false 
    ```

### Create grafana configuration
1. Create a new folder named **provisioning** the **grafana** folder. 
2. Create a new folder named **dashboards** in the **provisioning** folder and add a **dashboards.yaml** file with the following contents.

    ```
    apiVersion: 1

    providers:
    - name: 'monitoredapi'
    folder: 'monitoredapi'
    type: file
    options:
        path: /etc/grafana/provisioning/dashboards/monitoredapi
    ```
3. Create a new folder named **monitoredapi** in the **dashboards** folder and add a **MonitoredAPIDashboard.json** file with the following contents.


4. Create a new folder named **datasources** in the **provisioning** folder and add a **datasources.yaml** file with the following contents.

    ```
    apiVersion: 1

    datasources:
    - name: elasticsearch
        type: elasticsearch
        access: proxy
        database: "[logstash-]YYYY.MM.DD"
        url: http://elasticsearch:9200
        jsonData:
        interval: Daily
        timeField: "@timestamp"
        logLevelField: level
        logMessageField: line
        esVersion: 7.17.8
    ```

## Create Configuration for OpsServer

1. Create a new folder named **opserver** in the **deploy** folder.
2. Create a new folder named **config** in the **opserver** folder and add a **opserverSettings.json** file with the following contents.

    ```
    {
    "ForwardedHeaders": {
        "KnownNetworks": [ "10.0.0.0/16" ],
        "KnownProxies": [],
        "ForwardedHeaders": "All"
    },
    "Security": {
        "provider": "EveryonesAnAdmin"
    },
    "Modules": {
        /* Configuration for the SQL Server dashboard */
        "Sql": {
        "defaultConnectionString": "Server=$ServerName$;Database=monitoredapi;User Id=sa;Password=Pass@word;Encrypt=False",
        "refreshIntervalSeconds": 30,
        "instances": [
            {
            "name": "sqldata"
            }
        ]
        },
        /* Configuration for the Redis dashboard */
        "Redis": {
        },
        /* Configuration for the Elastic dashboard */
        "Elastic": {
        "clusters": [
            {
            "name": "docker-cluster",
            "refreshIntervalSeconds": 20,
            "nodes": [
                "ecb229eb0c90"
            ]
            }
        ]
        },
        /* Configuration for the Exceptions dashboard */
        "Exceptions": {
        "stackTraceReplacements": [
            {
            "name": "github",
            "pattern": "(?<= in )https?://raw.githubusercontent.com/([^/]+/)([^/]+/)([^/]+/)(.*?):line (\\d+)",
            "replacement": "<a href=\"https://github.com/$1$2blob/$3$4#L$5\">$4:line $5</a>"
            }
        ]
        }
    }
    }
    ```
