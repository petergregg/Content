---
title: Debug Container in Visual Studio Code Running in Docker 
author: Peter Gregg
description: Debug Container in Visual Studio Code Running in Docker
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/01/29 14:00:00
categories: 
  - Visual Studio Code
  - Docker
  - dotnet core
  - Web API
---

# Debug Container in Visual Studio Code Running in Docker
In this article, you will learn how to Debug Container in Visual Studio Running in Docker.

## Prerequisites
The following prerequisites will be required to complete this tutorial:
- Visual Studio Code with the C# and Docker extension installed. If you don't have Visual Studio Code installed, [download Visual Studio Code for free](https://code.visualstudio.com/download) before you begin.
- Windows Subsystem for Linux (WSL). If you don't have WSL installed, [download Docker WSL for free](https://learn.microsoft.com/en-us/windows/wsl/install) before you begin.
- Docker Desktop. If you don't have Docker Desktop installed, [download Docker Desktop for free](https://docs.docker.com/desktop/install/windows-install/) before you begin.
- dotnet core application with docker compose orchestration.

# Add Visual Studio Code Launch file
1. Open a dotnet core application in **Visual Studio Code**.
2. Select the **Run and Debug** icon, and then select **create a launch.json file**. 
    
    ![Visual Studio Code Create Launch JSON File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeCreateLaunchJSONFile.png)

3. Enter **D** into the search box and then select **Docker: Debug in Container**.

    ![Visual Studio Code Create Launch JSON File Debug Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeCreateLaunchJSONFileDebugDockerContainer.png)

4. Add the following JSON to the `launch` JSON file.

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

# Run Web APP in docker container
1. Add the following volume mapping to the service you would like to debug in the `docker.compose.override` yaml file `- ~/.vsdbg:/remote_debugger:rw`.

2. Select **Terminal** > **New Terminal**.

    ![Visual Studio Code Open New Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeOpenTerminal.png)

3. Run the following `docker compose up` command in the terminal.

    ```
    docker compose -f docker-compose.yml -f docker-compose.override.yml up -d --build --force-recreate
    ```

    ![Visual Studio Code Run Docker Compose Up in Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeRunDockerComposeUpInTerminal.png)

# Attach Debugger
1. Select **Run and Debug** tab, and then choose your dotnet core web application from the **RUN AND DEBUG** list. Select the **play** icon.

    ![Visual Studio Code Start Debugging Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeStartDebuggingDockerContainer.png)

3. Add a **breakpoint** to your dotnet core application and then send request to the application.

    ![Visual Studio Code Breakpoint Debugging Docker Container](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeDebuggingDockerContainer.png)

# Disconnect Debugger 
1. Select the **Disconnect** plug icon to disconnect the  debugger from the container. 

    ![Visual Studio Code Debugging Docker Container Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeDebuggingDockerContainerNavigation.png)
