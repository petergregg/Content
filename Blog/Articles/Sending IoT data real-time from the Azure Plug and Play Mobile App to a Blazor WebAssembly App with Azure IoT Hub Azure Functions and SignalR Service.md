---
title: Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub Azure Functions and SignalR Service
description: Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub Azure Functions and SignalR Service
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2021/07/12 19:00:00
categories: 
  - Azure IoT Hub
  - Azure IoT Plug and Play Mobile App
  - Azure Functions
  - Azurite
  - SignalR
  - Blazor
---

# Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub, Azure Functions and SignalR Service

In this article, you will learn how to build a solution to send IoT data real-time from a Plug and Play mobile app to a Blazor app with Azure IoT Hub, Azure Functions and a SignalR Service.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community with the ASP.NET and web development, and Azure development workloads. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- Azure account. If you don't have an Azure account, [create one for free](https://azure.microsoft.com/en-gb/free/) before you begin. 

## Create an Azure Functions Project in Visual Studio

1. Open **Visual Studio**.
2. Select **Create a new project**.

    ![Visual Studio Create New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateNewProject.png)

3. Search for **azure function**, and then select **Azure Functions**, and then select **Next**.

    ![Visual Studio Create New Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateANewAzureFunctionsProject.png)

4. Enter the following values in the **Configure your new project window**, and then select **Next**.

    | Parameter | Value |
    | --- | --- | 
    | Project Name | IOTHubIngestionSignalR | 
    | Location | Location of your choice  | 
    | Solution name | IOTHubIngestionSignalR  |
    | Place solution and project in the same directory | Unchecked  |

    ![Visual Studio Configure New Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureAzureFunctionsProject.png)

5. Enter the following values in **Additional information**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Functions worker | .NET 6.0 (Long-term support) | 
    | Function | IoT Hub trigger |
    | Enable Docker | Unchecked  |
    | Use Azurite for runtime storage account (AzureWebJobsStorage) | Checked  |
    | Connection string setting name | IoTHubEndpoint  |
    | Path | messages/events |

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureAzureFunctionsProjectAdditionalInformation.png)


# Create Azure IoT Hub and setup IoT Hub in Azure Function

## Create Azure IoT Hub

1. Navigate to portal azure
2. Create an Azure Hub 

## Update IoT Hub connection string in Visual Studio 

1. In Visual Studio, open the `local.settings` json file and replace `{YourConnectionString}` with the connections string you copied earlier.

    ```
        "IoTHubEndpoint": "{YourConnectionString}"
    ```

## Update Azure Functions project nuget 

1. Right click on the **IOTHubIngestionSignalR** project, and select **Manage Nuget Packages**

2. Select **Installed** and then **Microsoft.Azure.WebJobs.Extensions.EventHubs**. Select version **4.3.0** in the Version drop down list and then **Update**.

   ![Visual Studio Nuget Package Manager Update Microsoft Azure WebJobs Extensions EventHubs Nuget package](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerUpdateMicrosoftAzureWebJobsExtensionsEventHubs.png)

3. In the **Preview Changes** window select **OK**.

4. Run Azure Function.

   ![Visual Studio Run Azure Functions IoT SignalR Project After IoT Hub Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioRunAzureFunctionsIoTSignalRProjectAfterIoTHubSetup.png)

# Create Azure SignalR service and setup SignalR in Azure Function

1. Navigate to portal azure
2. Create an SignalR service 

## Add SignalR connection string in Visual Studio 

1. In Visual Studio, open the `local.settings` file and add the following. Replace `{YourConnectionString}` with the connection string copied earlier.
    ```
    "AzureSignalRConnectionString": "{YourConnectionString}"
    ```

2. In Visual Studio, right click on the `Function1` c# file and select **Rename**. Rename it to `IOTSignalRFunction`.

3. Replace the code in the `IOTSignalRFunction` with the following.
    ```
    public static async Task RunAsync(
                [IoTHubTrigger("messages/events", Connection = "IoTHubEndpoint")]EventData message,
                [SignalR(HubName = "IOTSampleData")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {

                var iOTDeviceMessage = Encoding.UTF8.GetString(message.Body.Array);
                log.LogInformation($"C# IoT Hub trigger function processed a message: {iOTDeviceMessage}");

                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "iotDeviceMessage",
                        Arguments = new[] { iOTDeviceMessage }
                    })
                    .ConfigureAwait(false);
            }
    ```

## Add SignalR negotiate function

1. In Visual Studio, right click on the **IOTHubIngestionSignalR** Azure Functions project, and select **Add**, and then **New Azure Function**.

    ![Visual Studio Add Azure Function To IoT Hub SignalR Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddAzureFunctionToIoTHubSignalRProject.png)

2. Select **Azure Function** and name the function **SignalRNegotiate** in the **Name** textbox. Select **Add**.

    ![Visual Studio Add Azure Function To Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddAzureFunctionToAzureFunctionsProject.png)

3. Select **SignalR** in the Azure Functions list, and Enter the following values into the New Azure Function window.

    | Parameter | Value |
    | --- | --- | 
    | Authorization level | Anonymous | 
    | Hub name | IOTSampleData |
    | Configure SignalR connection | Unchecked |

    ![Visual Studio Add SignalR Negotiate Azure Function To Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddSignalRNegotiateAzureFunctionToAzureFunctionsProject.png)

## Add New Blazor WebAssembly App Project

1. In Visual Studio, right click on the **IOTHubIngestionSignalR** solution, and select **Add**, and then **New Project**.

2. Search for **blazor**, and then select **Blazor WebAssembly App**, and then select **Next**.

    ![Visual Studio Add A New Blazor WebAssembly App Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateANewBlazorWebAssemblyAppProject.png)


3. Enter the following values in the **Configure your new project window**, and then select **Next**.

    | Parameter | Value |
    | --- | --- | 
    | Project Name | IOTDevices | 
    | Location | Location of your choice |

    ![Visual Studio Configure Blazor WebAssembly Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureBlazorWebAssemblyProject.png)

4. Enter the following values in **Additional information**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Framework | .NET 6.0 (Long-term support) | 
    | Authentication type | None  | 
    | Configure for HTTPS | Checked  |
    | ASP.NET Core Hosted | Unchecked  |
    | Progressive Web Application | Unchecked  |
    | Do not use top-level statements | Unchecked  |

    ![Visual Studio Configure Blazor WebAssembly App Project Additional Information](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureBlazorWebAssemblyAppProjectAdditionalInformation.png)

## Install Nuget packages

1. Right click on the **IOTHubDevices** project, and select **Manage Nuget Packages**.

2. Select **Browse** and then search for **Microsoft.AspNetCore.SignalR.Client**, and then **Install**.

    ![Visual Studio Nuget Package Manager Install Microsoft AspNetCore SignalR Client](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerInstallMicrosoftAspNetCoreSignalRClient.png)

3. In the **Preview Changes** window select **OK**.

4. Still in the NuGet Package Manager, select **Browse** and then search for **Microsoft.Extensions.Logging**, and then select **Install**.

    ![Visual Studio Nuget Package Manager Install Microsoft Extensions Logging](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerInstallMicrosoftExtensionsLogging.png)

5. In the **Preview Changes** window select **OK**.

## Enable CORS in Azure Function
1. In Visual Studio, open the `launchSettings` json file in the **Properties** folder of the **IOTDevices** project. Copy the `https` url in `applicationUrl` under `IOTDevices`.

2. In Visual Studio, open the `launchSettings` json file in the **Properties** folder of the **IOTHubIngestionSignalR** Azure function project. Add the following `--cors https://localhost:7145` to the end of `commandLineArgs`. Replace the `localhost:7145` with the blazor `applicationUrl` you copied earlier.

    ```
    {
        "profiles": {
            "IOTHubIngestionSignalR": {
            "commandName": "Project",
            "commandLineArgs": "--port 7178 --cors https://localhost:7145",
            "launchBrowser": false
            }
        }
    }
    ```

3. In Visual Studio, open the `local.settings` json file in the **IOTHubIngestionSignalR** Azure function project. Add the following block.

    ```
    "Host": {
        "LocalHttpPort": 7178,
        "CORS": "https://localhost:7145",
        "CORSCredentials": false
    }
    ```











    

















