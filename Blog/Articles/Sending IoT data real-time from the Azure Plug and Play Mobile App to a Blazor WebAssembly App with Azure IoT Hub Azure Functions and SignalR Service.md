---
title: Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub Azure Functions and a SignalR Service
description: Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub Azure Functions and a SignalR Service
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2023/07/22 13:10:00
categories: 
  - Azure IoT Hub
  - Azure IoT Plug and Play Mobile App
  - Azure Functions
  - Azurite
  - Azure SignalR Service
  - Blazor WebAssembly App
  - Visual Studio
---

# Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub, Azure Functions and a SignalR Service

In this article, you will learn how to build a solution to send IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub, Azure Functions and a SignalR Service.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Visual Studio 2022 Community with the ASP.NET and web development, and Azure development workloads. If you don't have Visual Studio installed, [download Visual Studio for free](https://visualstudio.microsoft.com/vs/community/) before you begin.
- Azure account. If you don't have an Azure account, [create one for free](https://azure.microsoft.com/en-gb/free/) before you begin. 

# Create an Azure Functions Project in Visual Studio

1. Open **Visual Studio**.
2. Select **Create a new project**.

    ![Visual Studio Create a New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateNewProject.png)

3. Search for **azure function**. Select **Azure Functions**, and then select **Next**.

    ![Visual Studio Create a New Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioCreateANewAzureFunctionsProject.png)

4. Enter the following values in the **Configure your new project window**, and then select **Next**.

    | Parameter | Value |
    | --- | --- | 
    | Project Name | IOTHubIngestionSignalR | 
    | Location | Location of your choice  | 
    | Solution name | IOTHubIngestionSignalR  |
    | Place solution and project in the same directory | Unchecked  |

    ![Visual Studio Configure a New Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureAzureFunctionsProject.png)

5. Enter the following values in **Additional information**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Functions worker | .NET 6.0 (Long-term support) | 
    | Function | IoT Hub trigger |   
    | Use Azurite for runtime storage account (AzureWebJobsStorage) | Checked  |
    | Enable Docker | Unchecked  |
    | Connection string setting name | IoTHubEndpoint  |
    | Path | messages/events |

    ![Visual Studio Additional Information New Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureAzureFunctionsProjectAdditionalInformation.png)


# Create an Azure IoT Hub and Connect it to the Azure Functions Project

## Create an Azure IoT Hub

1. Navigate to the [Azure Portal](https://portal.azure.com/), and then search for **iot hub**.

    ![Azure Search for IoT Hub](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureSearchIoTHub.png)

2. Select **+ Create**.

    ![Azure IoT Hub Navigation Bar Create Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubNavigationBar.png)

3. Enter the following values in the **Basics** tab. Select **Review + create**, and select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Subscription | Your Azure Subscription | 
    | Resource group | Create new with a name of your choice  | 
    | IoT hub name | Chosen name for the IoT hub name |
    | Region | Location nearest to you that has the IoT Hub |
    | Tier | Free |
    | Daily message limit | 8000 (US$0/month) |

    ![Azure IoT Hub Basics Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubBasicsSetup.png)

4. Navigate to your new IoT Hub and search for **built**. Select **Built-in endpoints**. Scroll down to the **Event Hub compatible endpoint** section, and copy the **Event Hub-compatible endpoint** value.

    ![Azure IoT Hub Built-in endpoints](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubBuiltInEndpoints.png)

## Update the IoT Hub Endpoint in the Azure Functions Project

1. In Visual Studio, open the `local.settings` json file. Replace `{YourIoTHubEndpoint}` with the endpoint you copied earlier in the [Azure Portal](https://portal.azure.com/).

    ```
    {
        "IsEncrypted": false,
        "Values": {
            ...
            "IoTHubEndpoint": "{YourIoTHubEndpoint}"
        }
    }
    ```

## Update the Azure Functions Project NuGet Packages 

1. Right click on the **IOTHubIngestionSignalR** project, and then select **Manage NuGet Packages...**.

    ![Visual Studio Manage NuGet packages](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioManageNuGetPackages.png)

2. Select **Installed**, and then  select **Microsoft.Azure.WebJobs.Extensions.EventHubs**. Select version **4.3.0** in the **Version** drop down list and then **Update**.

   ![Visual Studio NuGet Package Manager Update Microsoft Azure WebJobs Extensions EventHubs NuGet package](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerUpdateMicrosoftAzureWebJobsExtensionsEventHubs.png)

3. In the **Preview Changes** window, select **OK**.

4. Run the Azure Function.

   ![Visual Studio Run Azure Functions IoT SignalR Project After IoT Hub Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioRunAzureFunctionsIoTSignalRProjectAfterIoTHubSetup.png)

# Create an Azure SignalR Service and Connect it to the Azure Functions Project

1. Navigate to the [Azure Portal](https://portal.azure.com/), and then search for **signalr**.

    ![Azure Search for SignalR Service](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSearchSignalRService.png)

2. Select **+ Create**.

    ![Azure SignalR Service Navigation Bar Create Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSignalRServiceNavigationBar.png)

3. Enter the following values in the **Basics** tab. Select **Review + create**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Subscription | Your Azure Subscription | 
    | Resource group | Create new with a name of your choice  | 
    | Resource name | Chosen name for the SignalR service name |
    | Region | Location nearest to you that has the SignalR service |
    | Pricing tier | Free |
    | Service mode | Serverless |

    ![Azure SignalR Service Basics Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSignalRServiceBasicsSetup.png)

4. Navigate to the new SignalR service and then search for **connect**. Select **Connection strings**. Scroll down to the **For access key** section, and then copy the **Connection string** value.

    ![Azure SignalR Service Copy Connection String](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSignalRServiceConnectionStrings.png)

## Add SignalR Connection String to Azure Functions Project

1. In Visual Studio, open the `local.settings` file, and then add the following code. Replace `{YourConnectionString}` with the connection string copied earlier.

    ```
    {
        "IsEncrypted": false,
        "Values": {
            ...
            "AzureSignalRConnectionString": "{YourConnectionString}"
        }
    }
    ```

2. Right click on the `Function1` c# file. Select **Rename**, and then rename it to `IOTSignalRFunction`.

    ![Visual Studio Rename File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioRenameFile.png)

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

## Add SignalR Negotiate Function

1. In Visual Studio, right click on the **IOTHubIngestionSignalR** Azure Functions project. Select **Add**, and then select **New Azure Function**.

    ![Visual Studio Add Azure SignalR Negotiate Function To IoT Hub SignalR Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddAzureFunctionToIoTHubSignalRProject.png)

2. Select **Azure Function**. Name the function **SignalRNegotiate** in the **Name** box, and then select **Add**.

    ![Visual Studio Add Azure Function To Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddAzureFunctionToAzureFunctionsProject.png)

3. Select **SignalR** in the Azure Functions list. Enter the following values into the New Azure Function window, and then select **Add**.

    | Parameter | Value |
    | --- | --- | 
    | Authorization level | Anonymous | 
    | Hub name | IOTSampleData |
    | Configure SignalR connection | Unchecked |

    ![Visual Studio Add SignalR Negotiate Azure Function To Azure Functions Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddSignalRNegotiateAzureFunctionToAzureFunctionsProject.png)

# Add New Blazor WebAssembly App Project

1. In Visual Studio, right click on the **IOTHubIngestionSignalR** solution. Select **Add**, and then **New Project**.

    ![Visual Studio Add New Project To Solution](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioAddNewProjectToSolution.png)

2. Search for **blazor**. Select **Blazor WebAssembly App**, and then **Next**.

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

## Install NuGet Packages

1. Right click on the **IOTDevices** project, and then select **Manage NuGet Packages**.

2. Select **Browse** and then search for **Microsoft.AspNetCore.SignalR.Client**, and then select **Install**.

    ![Visual Studio NuGet Package Manager Install Microsoft AspNetCore SignalR Client](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerInstallMicrosoftAspNetCoreSignalRClient.png)

3. In the **Preview Changes** window, select **OK**.

4. Still in the NuGet Package Manager, select **Browse**. Search for **Microsoft.Extensions.Logging**, and then select **Install**.

    ![Visual Studio NuGet Package Manager Install Microsoft Extensions Logging](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerInstallMicrosoftExtensionsLogging.png)

5. In the **Preview Changes** window, select **OK**.

## Add Functionality to the Blazor WebAssembly App to Connect it to the Azure SignalR Service

1. In Visual Studio, open the`program` c# file in the **IOTDevices** project, and add the following code above `await builder.Build().RunAsync();`.

    ```
    ...

    builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });

    await builder.Build().RunAsync();
    ```

2. Open the `launchSettings` json file in the **Properties** folder of the **IOTHubIngestionSignalR** Azure function project, and copy the `--port` value in `commandLineArgs`.

    ```
    {
        "profiles": {
            "IOTHubIngestionSignalR": {
                ...
                "commandLineArgs": "--port 7178 --cors https://localhost:7145",
                "launchBrowser": false
            }
        }
    }
    ```

3. Right click on the **Pages** folder. Select **Add**, and then **Razor Component...**.

4. Name the Razor Component **FetchData** in the **Name** box, and select **Add**.

5. Add the following code into `FetchData`. Replace the `{YourPort}` in the url in following code with the `port` you copied earlier.

    ```
    @page "/fetchdata"
    @using Microsoft.AspNetCore.SignalR.Client
    @implements IAsyncDisposable

    <PageTitle>IOT Devices</PageTitle>

    <h1>IOT Devices</h1>

    <ul id="messagesList">
        @foreach (var message in messages)
        {
            <li>@message</li>
        }
    </ul>

    @code {
        private HubConnection? hubConnection;
        private List<string> messages = new List<string>();
        

        protected override async Task OnInitializedAsync()
        {
            hubConnection = new HubConnectionBuilder()
        .WithUrl("http://localhost:{YourPort}/api")
        .Build();

            hubConnection.On<string>("iotDeviceMessage", (message) =>
            {
                var encodedMsg = $"{message}";
                messages.Add(encodedMsg);
                StateHasChanged();
            });

            await hubConnection.StartAsync();
        }

        public bool IsConnected =>
                hubConnection?.State == HubConnectionState.Connected;

        public async ValueTask DisposeAsync()
        {
            if (hubConnection is not null)
            {
                await hubConnection.DisposeAsync();
            }
        }
    }
    ```

6. Open the `NavMenu` **Razor Component** in the **Shared** folder and add the following code before the closing `</nav>` tag.

    ```
    <div class="nav-item px-3">
        <NavLink class="nav-link" href="fetchdata">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Fetch data
        </NavLink>
    </div>
    ```

# Enable CORS in the Azure Functions Project
1. In Visual Studio, open the `launchSettings` json file in the **Properties** folder of the **IOTDevices** project. Copy the `https` url in `applicationUrl` under `IOTDevices`.

    ```
    {
        "profiles": {
            "IOTDevices": {
                ...
                "applicationUrl": "https://localhost:7145;http://localhost:5205"
        },
        ...
        }
    }
    ```

2. Open the `launchSettings` json file in the **Properties** folder of the **IOTHubIngestionSignalR** Azure function project. Add `--cors https://localhost:7145` to the end of `commandLineArgs`. Replace the `localhost:7145` with the `applicationUrl` you copied earlier.

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

3. Open the `local.settings` json file in the **IOTHubIngestionSignalR** Azure function project, and add the following code to the end of the file.

    ```
    "Host": {
        "LocalHttpPort": 7178,
        "CORS": "https://localhost:7145",
        "CORSCredentials": false
    }
    ```

# Install the Azure Plug and Play Mobile App

1. [Install the Azure Plug and Play mobile app](https://learn.microsoft.com/en-us/azure/iot/iot-phone-app-how-to#install-the-app) onto your device.

# Setup Your Device in Azure IoT Hub

1. Navigate to the **IoT Hub** in the [Azure Portal](https://portal.azure.com/). Search for **devices**, and then select **+ Add Device**.

    ![Azure IoT Hub Devices Add Device Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubDevicesAddDeviceNavigation.png)

2. Enter the following values in the **Create a device** window.

    | Parameter | Value |
    | --- | --- | 
    | Device ID | A device ID of your choice | 
    | IoT Edge Device | Unchecked | 
    | Authentication type | Symmetric key |
    | Auto-generate keys | Checked |
    | Connect this device to an IoT hub | Enable |
    | Parent device | No parent device |

    ![Azure IoT Hub Devices Create Device](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubDevicesCreateDevice.png)


3. Select the new device when its created in the **Devices** tab, and then copy the **Primary connection string**.

    ![Azure IoT Hub Devices Device Primary Connection String](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubDevicesDevicePrimaryConnectionString.png)

4. Open the **Azure Plug and Play mobile app** on your device. Select **Scan QR code** .
   
    ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPScanQRCode.jpeg)

5. Select the option to **Allow IoT PnP to take pictures and record video** with your devices camera.

   ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPAllowPictureTaking.jpeg)
   
6. Select **Connect Manually**.

    ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPConnectManuallyNavigation.jpeg)
   
7. Select **IoT Hub device connection string**. Enter the **connection string** you copied earlier, and then select **Connect**.

    ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPManuallyConnect.jpeg)

# Send Data from the Azure IoT Plug and Play Mobile Application to the Blazor WebAssembly App

1. In Visual Studio, right click on the **IOTHubIngestionSignalR**, solution and then select **Configure Startup Projects...**.

    ![Visual Studio Solution Configure Startup Projects](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioSolutionConfigureStartupProjects.png)

2. Select **Multiple startup projects**, and then **OK**. 

    ![Visual Studio Solution Configure Startup Projects](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioSolutionConfigureMultipleStartupProjects.png)

3. Select **Start**.

    ![Visual Studio Start Multiple Projects](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioStartMultipleProjects.png)

4. Select **Yes**.

    ![Visual Studio Trust ASP.NET Core SSL Certificate](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioTrustASPNETCoreSSLCertificate.png)

5. Select **Yes**.
    ![Visual Studio Trust ASP.NET Core SSL Certificate](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioTrustASPNETCoreSSLCertificateSecurityWarning.png)

6. Open the **Azure Plug and Play Mobile App** on your device.

7. Navigate to the **FetchData** page in the **Blazor WebAssembly App**.

    ![IoT Hub Ingestion SignalR Blazor WebAssembly FetchData Page](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/IoTHubIngestionSignalR/BlazorWebAssemblyFetchDataPage.png)










    

















