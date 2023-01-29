---
title: Structured logging with Serilog and Seq and Event Viewing with Elasticsearch Logstash Grafana and Opserver in Docker
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
  - Entity Framework core
  - dotnet core
  - Web API
---

# Structured logging with Serilog and Seq and Event Viewing with Elasticsearch, Logstash, Grafana and Opserver in Docker

In this article, you will learn how to add Structured logging to a dotnet core Web API project using Serilog and Seq and Event Viewing with Elasticsearch, Logstash, Grafana and Opserver in Docker containers. 

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community with the ASP.NET and web development workload. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.

## Create a Web API Project in Visual Studio with Docker Support

1. Open **Visual Studio**.
2. Select **Create a new project**.

    ![Visual Studio Create New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateNewProject.png)
3. Search for **core web api**, and then select **ASP.NET Core Web API**, and then select **Next**.

    ![Visual Studio Create New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateDotNetCoreWebAPIProject.png)

4. Enter the following values in **Configure your new project window**, and then select **Next**.

    | Parameter | Value |
    | --- | --- | 
    | Project Name | Monitored.API | 
    | Location | Location of your choice  | 
    | Solution name | MonitoredDockerStack  |
    | Place solution and project in the same directory | Unchecked  |

    ![Visual Studio Configure New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureDotNetCoreWebAPIProject.png)

5. Enter the following values in **Additional information**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Framework | .NET 6.0 (Long-term support) | 
    | Authentication type | None  | 
    | Configure for HTTPS | Checked  |
    | Enable Docker | Checked  |
    | Docker OS | Linux  |
    | Use controllers (uncheck to use minimal APIs) | Checked  |
    | Enable OpenAPI | Checked  |

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureDockerDotNetCoreWebAPIProject.png)

# Install the following nuget packages for Serilog, Seq, Elastic Search and MS SQL Database support
1. In Visual Studio, select **Tools** > **Nuget Package Manager** > **Package Manager Console**.

    ![Visual Studio Add Nuget Package Manager Console](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerConsole.png)

2. Enter the following.

    Install-Package Serilog.AspNetCore

    Install-Package Serilog.Sinks.Seq
    
    Install-Package Serilog.Sinks.ElasticSearch
    
    Install-Package Elastic.Apm.SerilogEnricher
    
    Install-Package Microsoft.EntityFrameworkCore.SqlServer
    
    Install-Package AutoFixture

## Enable Serilog in program.cs

1. Add the following code to the `program.cs` under `var builder = WebApplication.CreateBuilder(args);` and resolve any usings.

    ```
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
## Add Database to the project

### Add a model to define the database entity

1. Add the following model class `WeatherForecast` into the `Data` folder.

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

### Add a database context

1. Add the following class `MonitoredAPIDataContext` into the `Data` folder.

    ```
    public class MonitoredAPIDataContext : DbContext
    {
        public MonitoredAPIDataContext(DbContextOptions<MonitoredAPIDataContext> options) : base(options)
        {
        }

        public DbSet<WeatherForecast> WeatherForecasts { get; set; }
    }
    ```

### Seed the Database
1. Add the following class `Seeder` into the `Data` folder.

    ```
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
    ```

### Register the database context
1. Add the following code to the `program.cs` class after the `Use.serilog` configuration block. 

    ```
    builder.Services.AddDbContext<MonitoredAPIDataContext>(options =>
        options.UseSqlServer(builder.Configuration.GetConnectionString("monitoredapidb")));
    ```
## Replace the code in WeatherController.cs

1. Replace the code in the `WeatherController` with the following. 

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
        
## Replace App Settings JSON

1. Replace the JSON in the `appsettings` and `appsettings.Development` with the following.

    ```
    {
    "AllowedHosts": "*",
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
            "System": "Warning",
            "Microsoft.Hosting.Lifetime": "Information"
        }
        }
    },
    "ElasticSearch": {
        "ServerUrl": "http://elasticsearch:9200"
    },
    "ConnectionStrings": {
        "monitoredapidb": "Server=sqldata;Database=monitoredapi;User Id=sa;Password=Pass@word;Encrypt=False"
    }
    }
    ```

# Add Docker Compose Project to the Solution

1. Right click on the **Web API project**, and then select **Add** > **Docker Support**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolution.png)

2. Select **Docker Compose**, and then **OK**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolutionOrchestrator.png)

3. Select **Linux**, and then **OK**.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddDockerComposeProjectToSolutionTargetOS.png)

4. A **docker-compose** project will be added to the solution.

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioDockerComposeProjectInSolution.png)

## Define Container Applications in Docker Compose

1. Add the following code to the `docker-compose` yaml file after the `monitored.api` service block.

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
2. Add the following yaml to the `docker-compose.override` yaml file after the `monitored.api` service block.

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
## Create configuration for ElasticSearch, Logstash and Grafana (ELG)

### Create ELG folder structure

1. Create a **deploy** folder in the root of the solution with the following folders in it **elasticsearch**, **logstash** and **grafana**.


    ![Visual Studio ELG Folder Structure](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioELGFolderStructure.png)

### Create elasticsearch configuration

1. Create a new folder named **config** in the **elasticsearch** folder and add the following yaml file `elasticsearch`.
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
1. Create a new folder named **provisioning** in the **grafana** folder. 
2. Create a new folder named **dashboards** in the **provisioning** folder and add the following yaml file `dashboards`.

    ```
    apiVersion: 1

    providers:
    - name: 'monitoredapi'
    folder: 'monitoredapi'
    type: file
    options:
        path: /etc/grafana/provisioning/dashboards/monitoredapi
    ```
3. Create a new folder named **monitoredapi** in the **dashboards** folder and add the following JSON file `MonitoredAPIDashboard`.


4. Create a new folder named **datasources** in the **provisioning** folder and add the following yaml file **datasources**.

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
2. Create a new folder named **config** in the **opserver** folder and add the following JSON file `opserverSettings`.

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

## Folder Structure and config files for ELG and OPServer
1. After completing the steps above the final folder structure and config files will look like the following.
    
    ![Visual Studio ELG and OPServer Final Folder and Config Structure](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioELGOPServerFinalFolderConfigStructure.png)

# Testing
## Run Docker Stack

1. Right click on the `MonitoredDockerStack` Solution and select **Open in Terminal**

    ![Visual Studio Solution Open In Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioSolutionOpenInTerminal.png)

2. Run the following command in the terminal.

    ```
    docker compose -f docker-compose.yml -f docker-compose.override.yml up -d --build --force-recreate
    ```

    ![Visual Studio Solution Run Docker Compose Up in Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioSolutionRunDockerComposeUpInTerminal.png)

## Test Web API
1. Browse to [https://localhost:5021/](https://localhost:5021/)swagger/index.html.
2. Select **GET /WeatherForecast**, and then select **Try it out**.

    ![Swagger Try it out](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Swagger/SwaggerTryItOut.png)

3. Select **Execute**.

    ![Swagger Execute GET](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Swagger/SwaggerExecuteGET.png)

4. The following response with be displayed.

    ![Swagger GET Success Response](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Swagger/SwaggerGetSuccessResponse.png)

## Test Grafana

1. Browse to [http://localhost:3000/](http://localhost:3000/).
2. Enter the default username and password **admin / admin**.

    ![Grafana Login](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Grafana/GrafanaLogin.png)

3. In Navigation on the left of the screen, select **Dashboards** icon.    

    ![Grafana Login](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Grafana/GrafanaDashboardsNavigationIcon.png)

4. Select **Monitored.API Overview**.

    ![Grafana Login](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Grafana/GrafanaDashboardsOverview.png)

5. View the Monitored.API Overview Dashboard.

    ![Grafana Dashboard Monitored.API Overview](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Grafana/GrafanaDashboardMonitoredAPIOverview.png)

## Test OPServer

1. Browse to [http://localhost:4001/](http://localhost:4001/). 
2. Enter the default username and password **admin / admin**.

    ![Opserver Login](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Opserver/OpserverLogin.png)

3. Select **SQL** in top right hand corner of the screen for the SQL overview.

    ![Opserver SQL Overview](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Opserver/OpserverSQLOverview.png)

4. Select **Elastic** in top right hand corner of the screen for the Elastic overview.

    ![Opserver SQL Overview](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Opserver/OpserverElasticOverview.png)









