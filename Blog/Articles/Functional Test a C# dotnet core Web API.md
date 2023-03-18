---
title: Functional Test a C# dotnet core Web API
author: Peter Gregg
description: Functional Test a C# dotnet core Web API
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/03/18 09:40:00
categories: 
  - Visual Studio
  - Integration Tests
  - Functional Tests
  - dotnet core
  - Web API
---

# Functional Test a C# dotnet core Web API

In this article, you will learn how to functional test a dotnet core Web API project with an SQL database using Visual Studio and xUnit. 

[Source code on GitHub](https://github.com/petergregg/MonitoringDockerStack)


## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community with the ASP.NET and web development workload. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- A dotnet core Web API project. If you don't have a project you can [follow the first step in these instructions to create one](https://www.pgdevopstips.co.uk/article/structured-logging-with-serilog-and-seq-and-event-viewing-with-elasticsearch-logstash-grafana-and-opserver-in-docker).


## Add a Functional Test Project to an existing Web API project

1. Open a dotnet core Web API project in **Visual Studio**.
2. Right click on the **Solution**, and then select **Add** > **New Project**.     

    ![Visual Studio Create New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddNewProjectToSolution.png)


2. Search for **xUnit**, and then select **xUnit Test Project**, and then select **Next**.

    ![Visual Studio Create New xUnit Test Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddANewXUnitProject.png)

3. Enter the following values in the **Configure your new project window**, and then select **Next**.

    | Parameter | Value |
    | --- | --- | 
    | Project Name | Monitored.FunctionalTests | 
    | Location | Location of your choice  | 

    ![Visual Studio Configure New xUnit Test Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureXUnitTestProject.png)

4. Enter the following values in **Additional information**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Framework | .NET 6.0 (Long-term support) | 

    ![Visual Studio Additional Information New xUnit Test Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureXUnitTestProjectAdditionalInformation.png)

# Install Web API Testing nuget packages

1. In Visual Studio, select **Tools** > **Nuget Package Manager** > **Package Manager Console**.

    ![Visual Studio Add Nuget Package Manager Console](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerConsole.png)

2. Enter the following.
    ```
    Install-Package Microsoft.AspNetCore.Mvc.Testing -Version 6.0.14
    ```

## Add a Basic Functional Test

### Expose the Web Api Project to the Test Project

1. Add the following code to the `program.cs` class after `app.Run` in the Web Api project. 

    ```
    public partial class Program { }
    ```

### Add a Custom WebApplicationFactory to the Test Project

1. Add the following class `CustomWebApplicationFactory` into a new `Base` folder, replacing `{YourConnectionString}` with your connection string.

    ```
    public class CustomWebApplicationFactory<TProgram>
        : WebApplicationFactory<TProgram> where TProgram : class
    {
        protected override void ConfigureWebHost(IWebHostBuilder builder)
        {
            builder.ConfigureServices(services =>
            {
                var dbContextDescriptor = services.SingleOrDefault(
                    d => d.ServiceType ==
                        typeof(DbContextOptions<MonitoredAPIDataContext>));

                services.Remove(dbContextDescriptor);

                var dbConnectionDescriptor = services.SingleOrDefault(
                    d => d.ServiceType ==
                        typeof(DbConnection));

                services.Remove(dbConnectionDescriptor);

                // Create open SqlConnection so EF won't automatically close it.
                services.AddSingleton<DbConnection>(container =>
                {
                    var connection = new SqlConnection("{YourConnectionString}");
                    connection.Open();

                    return connection;
                });

                services.AddDbContext<MonitoredAPIDataContext>((container, options) =>
                {
                    var connection = container.GetRequiredService<DbConnection>();
                    options.UseSqlServer(connection);
                });
            });

            builder.UseEnvironment("Development");
        }
    }
    ```

2. Add the following class `WeatherForecastScenarios` into the root of the test project.

    ```
    public class WeatherForecastScenarios : IClassFixture<CustomWebApplicationFactory<Program>>
    {
        private readonly HttpClient _client;
        private readonly CustomWebApplicationFactory<Program> _factory;
        public WeatherForecastScenarios(CustomWebApplicationFactory<Program> factory)
        {
            _factory = factory;
            _client = factory.CreateClient(new WebApplicationFactoryClientOptions
            {
                AllowAutoRedirect = false
            });
        }

        [Fact]
        public async Task Get_get_filtered_weather_forecasts_and_response_ok_status_code()
        {
            // Act
            var response = await _client.GetAsync("WeatherForecast?take=10&skip=0");

            // Assert
            response.EnsureSuccessStatusCode();
        }

    }
    ```

2. The Final Functional Test Project should look like the following.

    ![Visual Studio Final Test Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioFinalBasicFunctionalTestProject.png)

# Run Functional Tests

1. In Visual Studio, select **Test**, and then **Test Explorer**. 

    ![Visual Studio Open Test Explorer](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioOpenTestExplorer.png)

2. In the Test Explorer, select ![Run All Tests In View](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioTestExplorerRunAllTestsButton.png) button.


    ![Visual Studio Test Explorer Initial Not Run Tests](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioTestExplorerInitialNotRunTests.png)

3. Once the tests have run, you will see that all tests pass.

    ![Visual Studio Test Explorer Run All Tests Passed](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioTestExplorerRunAllTestsPassed.png)