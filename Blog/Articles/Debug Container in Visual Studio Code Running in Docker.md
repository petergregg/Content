---
title: Debug Container in Visual Studio Code Running in Docker 
author: Peter Gregg
description: Debug a container in Visual Studio Code running in Docker
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/01/30 20:45:00
categories: 
  - Visual Studio Code
  - Docker
  - Containers
  - Debugging
  - dotnet core
  - Web API
---

# Debug a container in Visual Studio Code running in Docker
In this article, you will learn how to debug a container in Visual Studio Code running in Docker.

## Prerequisites
The following prerequisites will be required to complete this tutorial:
- Visual Studio Code with the C# and Docker extension installed. If you don't have Visual Studio Code installed, [download Visual Studio Code for free](https://code.visualstudio.com/download) and install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and [Docker extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) before you begin.
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.
- dotnet core web application or api with docker compose orchestration.

# Add Visual Studio Code launch JSON file
1. Open a dotnet core application in **Visual Studio Code**.
2. Select the **Run and Debug** icon, and then select **create a launch.json file**. 
    
    ![Visual Studio Code Create Launch JSON File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeCreateLaunchJSONFile.png)

3. Enter **D** into the search box and then select **Docker: Debug in Container**.

    ![Visual Studio Code Create Launch JSON File Debug Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeCreateLaunchJSONFileDebugDockerContainer.png)

4. Add the following JSON to the `launch` JSON file, replacing the following values.

    | Parameter | Value | 
    | --- | --- | 
    | name | Display name of your choice | 
    | containerName | Your running container | 
    | sourceFileMap (left path) | Path where the application code / dll is published on the docker container (this will be defined in your docker file) | 
    | sourceFileMap (right path) | Path of the project in Visual Studio code workspace | 

    ```
    {
      "version": "0.2.0",
      "configurations": [
        {
          "name": "Monitored.API",
          "type": "docker",
          "request": "attach",
          "containerName": "monitoredapi",
          "platform": "netCore",
          "sourceFileMap": {
            "/src/Monitored.API": "${workspaceFolder}/Monitored.API"
          },
          "netCore": {
            "debuggerPath": "/remote_debugger/vsdbg"
          }
        }
      ]
    }
    ```

# Configure docker compose and run the web application or api in a docker container
1. Add the following volume mapping to the service you would like to debug in the `docker.compose.override` yaml file `- ~/.vsdbg:/remote_debugger:rw`.

2. Select **Terminal** > **New Terminal**.

    ![Visual Studio Code Open New Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeOpenTerminal.png)

3. Run the following `docker compose up` command in the terminal.

    ```
    docker compose -f docker-compose.yml -f docker-compose.override.yml up -d --build --force-recreate
    ```

    ![Visual Studio Code Run Docker Compose Up in Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeRunDockerComposeUpInTerminal.png)

# Debug web application or api in container
1. Select the **Run and Debug** icon, and then choose your dotnet core web application or api from the **RUN AND DEBUG** drop down list. Select the **Start Debugging (play)** icon.

    ![Visual Studio Code Start Debugging Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeStartDebuggingDockerContainer.png)

2. Add a **breakpoint** to your application or api and then send a request that will hit the breakpoint.

    ![Visual Studio Code Breakpoint Debugging Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeDebuggingDockerContainer.png)

# Disconnect Debugger 
1. Select the **Disconnect plug** icon to disconnect the debugger from the container. 

    ![Visual Studio Code Debugging Docker Container  Disconnect Plug](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeDebuggingDockerContainerNavigation.png)
