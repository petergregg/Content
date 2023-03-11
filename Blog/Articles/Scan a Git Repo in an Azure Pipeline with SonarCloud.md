---
title: Scan a Git Repo in an Azure Pipeline with SonarCloud
author: Peter Gregg
description: Scan a Git Repo in an Azure Pipeline with SonarCloud
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2023/03/11 10:00:00
categories: 
  - Azure DevOps
  - Azure Pipelines
  - SonarCloud Analysis
  - SonarCloud Scan Code
  - Git
---

# Scan Git Repo in an Azure Pipeline with SonarCloud

In this article, you will learn how to scan a Git repo in an Azure Pipeline with SonarCloud.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Azure DevOps account. If you don't have an Azure DevOps account,  [create one for free](https://azure.microsoft.com/en-us/products/devops/) before you begin.
- Azure DevOps project, with a dotnet core project in a Git repository. If you don't have a repo [Fork the articles Git repo](https://twogsdev.visualstudio.com/_git/CoreDeployTest).
- SonarCloud account. If you don't have a SonarCloud account,  [create one for free with your Azure DevOps account](https://www.sonarsource.com/products/sonarcloud/signup/) before you begin.

## Create a SonarCloud Organization and Link it to Azure DevOps

### Create a Personal Access Token
1. In Azure DevOps, select **User Settings**, **Personal access tokens** in the top right of the screen.

    ![Azure DevOps User Settings and Personal Access Token Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsUserSettingsPersonalAccessTokens.png)

2. Select **+ New Token**. 
    
    ![Azure DevOps Personal Access Tokens + New Token](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPersonalAccessTokensNavigation.png)

3. Enter the following values.

    | Parameter | Value |
    | --- | --- | 
    | Name | SonarCloud | 
    | Organization | Your Organization |
    | Scopes| Custom defined | 
    | Code | Read & Write |

    ![Azure DevOps SonarCloud Personal Access Token Values](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSonarCloudCreatePersonalAccessToken.png)

### Link SonarCloud to Azure DevOps with Personal Access Token 
1. Login to [SonarCloud](https://sonarcloud.io/login).
2. Select **Import project from Azure**, and then enter the following values, and then select **Continue**.

    | Parameter | Value |
    | --- | --- | 
    | Azure DevOps organization name | Your Organization name | 
    | Personal Access Token (PAT) | [PAT created earlier](#create-a-personal-access-token) |

    ![SonarCloud Create Organization](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudCreateAnOrganization.png)

3. Under **Import Organization details**, enter a **Name** and **Key** which will be used to identify your organization in Azure Pipelines, and then select **Continue**.

    ![SonarCloud Create Organization Import Organization](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudCreateAnOrganizationImportOrganization.png)

4. Select **Free plan** and then **Create Organization**.

    ![SonarCloud Create Organization Choose Free Plan](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudCreateAnOrganizationFreePlan.png)

## Analyze an Azure DevOps project in SonarCloud
1. In the root of a SonarCloud Organization, select **Projects**, and then **Analyze a new project**.

    ![SonarCloud Analyze a New Project](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudAnalyzeANewProject.png)

2. Select the **Project**, and then **Set Up**.

    ![SonarCloud Select a Repo to Analyze](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudSelectRepoToAnalyze.png)

3. Select the analysis method **With Azure DevOps Pipelines**.

   ![SonarCloud Analyze with Azure DevOps Pipelines](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudAzureDevOpsAnalysisMethod.png)

>You will now be presented with instructions to setup the analysis which will be outlined in the following sections. Keep the instructions open as you will need information from them.

### Add SonarCloud Extension to Azure DevOps

1. In the root of an Azure DevOps Organization, select **Organization Settings** in the bottom left of the screen.

    ![Azure DevOps Organization Settings](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsOrganisationSettings.png)

2. In the **General** section, select **Extensions**.

    ![Azure DevOps Organization Settings General Extensions Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsOrganisationSettingsGeneralExtensionsNavigation.png)

3. Select **Browse marketplace**.

    ![Azure DevOps Extensions Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsExtensionsNavigationBar.png)

4. Search for **SonarCloud**, and then select **SonarCloud**.

    ![Azure DevOps Search SonarCloud](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSonarCloudExtension.png)

5. Select **Get if free**, and then select **Install**.

    ![Azure DevOps SonarCloud Extension Get It Free](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSonarCloudExtensionGetItFree.png)

6. In the **Extensions** section, **SonarCloud** is now installed.

    ![Azure DevOps SonarCloud Extension Installed](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSonarCloudExtensionInstalled.png)

### Add SonarCloud Service Connection to an Azure DevOps Project

1. From the root of an Azure DevOps Project, select **Project settings** in the bottom left of the screen.

    ![Azure DevOps Projects Settings Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsProjectSettings.png)

2. In the **Pipelines** section, select **Service connections**. 

    ![Azure DevOps Project Settings Service Connections Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsProjectSettingsServiceConnectionsNavigation.png)

3. Select **Create service connection**.

    ![Azure DevOps Project Create Service Connection](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsCreateServiceConnection.png)

4. Search for **SonarCloud**, then select **Next**.

    ![Azure DevOps Service Connection Search SonarCloud](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsServiceConnectionSearchSonarCloud.png)

5. Enter the following values, and then select **Verify and Save**.

    | Parameter | Value |
    | --- | --- | 
    | SonarCloud Token | Your SonarCloud Token see below for instructions | 
    | Service connection name | SonarCloud | 
    | Grant access permission to all pipelines | True |

    ![Azure DevOps SonarCloud Service Connection Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsServiceConnectionSonarCloudSetup.png)

    > Copy and paste the **SonarCloud Token** from the instructions **Use this token** under the **Add a new SonarCloud Service Endpoint** section into Azure DevOps Service Connection Setup **SonarCloud Token** text box.
    ![SonarCloud Token](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudAzureDevOpsServiceConnectionToken.png) 

### Configure Azure DevOps Pipeline with SonarCloud Code Analysis

1. In the root of an Azure DevOps Project, select **Pipelines**.

    ![Azure DevOps Pipelines Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNavigation.png)

2. Select **Create Pipeline**.

    ![Azure DevOps Pipelines Create Your First Pipeline](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesCreateYourFirstPipeline.png)

3. In the **Connect** tab, Select **Azure Repos Git**.
    
    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesConnectAzureReposGit.png)

5. In the **Select** tab, select **ASP.NET Core** YAML template.

    ![Azure DevOps Pipelines Select ASP.NET Core YAML template](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNETCoreYamlTemplate.png)

6. Go to the SonarCloud instructions, select **.NET** build, under the **Configure Azure Pipeline** section.

    ![SonarCloud Instructions Select .NET Build](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudInstructionsNETBuild.png)

6. Replace the Contents of the YAML file with the following YAML replacing `{Your...}` with your organization, project key and project name from the SonarCloud instructions.

    ```
    trigger:
    - master

    pool:
    vmImage: ubuntu-latest

    variables:
    twogsdevProjectVersion: '1.0.0-$(Build.BuildNumber)'

    steps:
    - task: SonarCloudPrepare@1
        inputs:
        SonarCloud: 'SonarCloud'
        organization: 'twogsdev'
        scannerMode: 'MSBuild'
        projectKey: 'twogsdev_CoreDeployTest'
        projectName: 'CoreDeployTest'
        projectVersion: '$(twogsdevProjectVersion)'
    - task: DotNetCoreCLI@2
        inputs:
        command: 'build'
    - task: SonarCloudAnalyze@1
    - task: SonarCloudPublish@1
        inputs:
        pollingTimeoutSec: '300'
    ```  
7. Rename the pipelines YAML file to **sonarcloud-az-pipeline.yml** and then select **Save and run**.

    ![Azure DevOps Pipelines Rename the Pipelines Yaml File, Save and Run](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSaveAndRunNavigation.png)

8. Add a commit message and then select **Save and run**. 

    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSonarCloudCommitSaveAndRun.png)

9. Once the build has completed, in the Build Summary, select **Extensions**, and then **Detailed SonarCloud Report**.

    ![Azure DevOps Pipelines SonarCloud Report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSonarCloudReport.png)

>In order to see the Quality Gate result in Azure DevOps Pipelines. A Code Definition needs configuring in SonarCloud. Follow the next steps to set this up. 

## Setup Quality Gate in SonarCloud  

1. In the SonarCloud **Project Overview**, select **Set New Code definition**. 

    ![SonarCloud Report Quality Gate Set New Code Definition](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudQualityGateSetNewCodeDefinitionNavigation.png)

2. Select [**Previous Version**](https://docs.sonarcloud.io/improving/new-code-definition/), this corresponds to the `projectVersion` added to the pipeline YAML earlier. 

    ![SonarCloud Report Quality Gate Set New Code Definition](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudQualityGateSetNewCodeDefinitionSetup.png)

3. Once configured, in Azure DevOps Pipelines, select **Run new**. Once the Azure Pipeline has finished building and analyzing, select **Detailed SonarCloud Report**.

   ![Azure DevOps Pipelines Run New Job](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSonarCloudReport.png)

4. In SonarCloud select **See Full Analysis**.

    ![SonarCloud Report Quality Gate See Full Analysis](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudQualityGateSeeFullAnalysisNavigation.png)

4. The **Detailed Report** will be opened.

    ![SonarCloud Detailed Report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudDetailedReport.png)

## Analyze SonarCloud Report

1. In the SonarCloud Dashboard, select the **Your Project** > **See Full Analysis**, then **Overall Code**.

    ![SonarCloud Detailed Report OverallCode Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudDetailedReportOverallCodeNavigation.png)

2. Under **Reliability**, you will see a bug has been found. Select the **Number** next to **Bugs**.

    ![SonarCloud Detailed Report Overall Code Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudDetailedReportOverallCode.png)

3. The details of the Bug will be opened.

    ![SonarCloud Detailed Report Bug Details](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudDetailedReportBugDetails.png)

3. Select the **Bug** to see the code.

    ![SonarCloud Bug Code](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/SonarCloud/SonarCloudDetailedReportBugCode.png)


















