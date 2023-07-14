---
title: Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub Azure Functions and SignalR Service
description: Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub Azure Functions and SignalR Service
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/07/12 19:00:00
categories: 
  - Azure IoT Hub
  - Azure IoT Plug and Play Mobile App
  - Azure Functions
  - Azurite
  - Azure SignalR Service
  - Blazor WebAssembly
---

# Sending IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub, Azure Functions and SignalR Service

In this article, you will learn how to build a solution to send IoT data real-time from the Azure Plug and Play Mobile App to a Blazor WebAssembly App with Azure IoT Hub, Azure Functions and a SignalR Service.

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
    | Use Azurite for runtime storage account (AzureWebJobsStorage) | Checked  |
    | Enable Docker | Unchecked  |
    | Connection string setting name | IoTHubEndpoint  |
    | Path | messages/events |

    ![Visual Studio Additional Information New dotnet Core Web API Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioConfigureAzureFunctionsProjectAdditionalInformation.png)


# Create Azure IoT Hub and configure IoT Hub in Azure Function

## Create Azure IoT Hub

1. Navigate to the [Azure Portal](https://portal.azure.com/), search for **IoT Hub**.

    ![Azure Search for IoT Hub](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureSearchIoTHub.png)

2. Select **+ Create**.

    ![Azure IoT Hub Navigation Bar Create Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubNavigationBar.png)

3. Enter the following values in the **Basics** tab, then select the **Review + create** tab, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Subscription | Your Azure Subscription | 
    | Resource group | Create new with a name of your choice  | 
    | IoT hub name | Chosen name for the IoT hub name |
    | Region | Location nearest to you that has the IoT Hub |
    | Tier | Free |
    | Daily message limit | 8000 (US$0/month) |

    ![Azure IoT Hub Basics Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubBasicsSetup.png)

4. Navigate to your new IoT Hub and search for **built**. Select **Built-in endpoints**, scroll down to the **Event Hub compatible endpoint** section and copy the **Event Hub-compatible endpoint** value.

    ![Azure IoT Hub Built-in endpoints](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubBuiltInEndpoints.png)

## Update IoT Hub Endpoint in Azure Functions Project

1. In Visual Studio, open the `local.settings` json file and replace `{YourIoTHubEndpoint}` with the endpoint you copied earlier.

    ```
        "IoTHubEndpoint": "{YourIoTHubEndpoint}"
    ```

## Update Azure Functions project nuget 

1. Right click on the **IOTHubIngestionSignalR** project, and select **Manage Nuget Packages**

2. Select **Installed** and then **Microsoft.Azure.WebJobs.Extensions.EventHubs**. Select version **4.3.0** in the Version drop down list and then **Update**.

   ![Visual Studio Nuget Package Manager Update Microsoft Azure WebJobs Extensions EventHubs Nuget package](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioNugetPackageManagerUpdateMicrosoftAzureWebJobsExtensionsEventHubs.png)

3. In the **Preview Changes** window select **OK**.

4. Run Azure Function.

   ![Visual Studio Run Azure Functions IoT SignalR Project After IoT Hub Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudio/VisualStudioRunAzureFunctionsIoTSignalRProjectAfterIoTHubSetup.png)

# Create Azure SignalR service and configure SignalR in Azure Function

1. Navigate to the [Azure Portal](https://portal.azure.com/), and then search for **signalr**.

    ![Azure Search for SignalR Service](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSearchSignalRService.png)

2. Select **+ Create**.

    ![Azure SignalR Service Navigation Bar Create Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSignalRServiceNavigationBar.png)

3. Enter the following values in the **Basics** tab, then select the **Review + create** tab, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Subscription | Your Azure Subscription | 
    | Resource group | Create new with a name of your choice  | 
    | Resource name | Chosen name for the SignalR service name |
    | Region | Location nearest to you that has the SignalR service |
    | Pricing tier | Free |
    | Unit count | 1 |
    | Service mode | Serverless |

    ![Azure SignalR Service Basics Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSignalRServiceBasicsSetup.png)

4. Navigate to the new SignalR service and search for **connect**. Select **Connection strings**, scroll down to the **For access key** section and copy the **Connection string** value.

    ![Azure SignalR Service Connection Strings](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureSignalRService/AzureSignalRServiceConnectionStrings.png)

## Add SignalR connection string to Azure Functions project

1. In Visual Studio, open the `local.settings` file and add the following. Replace `{YourConnectionString}` with the connection string copied earlier.
    ```
    "AzureSignalRConnectionString": "{YourConnectionString}"
    ```

2. Right click on the `Function1` c# file and select **Rename**. Rename it to `IOTSignalRFunction`.

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

## Add functionality and Page to Blazor WebAssembly App to connect to Azure SignalR Service

1. In Visual Studio, in the **IOTDevices** project, open `program` c# file and add the following code above `await builder.Build().RunAsync();`.

    ```
    builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });
    ```

2. Open the `launchSettings` json file in the **Properties** folder of the **IOTHubIngestionSignalR** Azure function project, and copy the `--port` value in `commandLineArgs`.

3. Right click on the **Pages** folder, and select **Add** and then **Razor Component...**.

4. Name the Razor Component **FetchData** in the **Name** textbox, and select **Add**.

5. Add the following code into FetchData. Replace the port in the following url with the port you copied earlier `http://localhost:{YourPort}/api`.

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

5. Open the `NavMenu` razor component in the **Shared** folder and add the following code before the closing `</nav>` tag.

    ```
    <div class="nav-item px-3">
        <NavLink class="nav-link" href="fetchdata">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Fetch data
        </NavLink>
    </div>
    ```

## Enable CORS in Azure Function
1. In Visual Studio, open the `launchSettings` json file in the **Properties** folder of the **IOTDevices** project. Copy the `https` url in `applicationUrl` under `IOTDevices`.

2. Open the `launchSettings` json file in the **Properties** folder of the **IOTHubIngestionSignalR** Azure function project. Add the following `--cors https://localhost:7145` to the end of `commandLineArgs`. Replace the `localhost:7145` with the blazor `applicationUrl` you copied earlier.

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

3. Open the `local.settings` json file in the **IOTHubIngestionSignalR** Azure function project. Add the following block.

    ```
    "Host": {
        "LocalHttpPort": 7178,
        "CORS": "https://localhost:7145",
        "CORSCredentials": false
    }
    ```

## Install the Azure Plug and Play mobile app

1. [Install the Azure Plug and Play mobile app](https://learn.microsoft.com/en-us/azure/iot/iot-phone-app-how-to#install-the-app) onto your device.

## Setup device in Azure IoT Hub

1. Navigate to the IoT Hub in [Azure Portal](https://portal.azure.com/) and search for **devices**. Select **+ Add Device**.

    ![Azure IoT Hub Devices Add Device Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubDevicesAddDeviceNavigation.png)

2. Add a name into the **Device ID** box, and select **Save**.

3. Enter the following values in the **Create a device**. 

    | Parameter | Value |
    | --- | --- | 
    | Device ID | A device ID of your choice | 
    | IoT Edge Device | Unchecked | 
    | Authentication type | Symmetric key |
    | Auto-generate keys | Checked |
    | Connect this device to an IoT hub | Enable |
    | Parent device | No parent device |

    ![Azure IoT Hub Devices Create Device](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubDevicesCreateDevice.png)


4. Select the new the new device when created in **Devices**, and copy the **Primary connection string**.

    ![Azure IoT Hub Devices Device Primary Connection String](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTHub/AzureIoTHubDevicesDevicePrimaryConnectionString.png)

5. Open the Azure Plug and Play mobile on your device. Select **Scan QR code** .
   
    ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPScanQRCode.jpeg)

7. Select the option to allow iot pnp to use the devices camera.
   ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPAllowPictureTaking.jpeg)
9. Select **Connect Manually**.
    ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPConnectManuallyNavigation.jpeg)
11. Select **IoT Hub device connection string**, and then enter the connection string you copied earlier. Select **Connect**.
    ![Azure IoT PnP Scan QR Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureIoTPnP/AzureIoTPnPManuallyConnect.jpeg)










    

















