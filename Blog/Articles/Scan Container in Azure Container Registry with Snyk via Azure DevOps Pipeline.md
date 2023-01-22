---
title: Scan Container in Azure Container Registry with Snyk via Azure DevOps Pipeline
author: Peter Gregg
description: Scan Container in Azure Container Registry with Snyk via Azure DevOps Pipeline
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2022/01/21 13:00:00
categories: 
  - Azure Container Registry
  - Azure DevOps
  - Snyk
  - Docker
  - Containers
  - DevSecOps
---

# Scan Container in Azure Container Registry with Snyk via Azure DevOps Pipeline

In this article, you will learn how to scan a  container image in Azure Container Registry with Snyk via Azure DevOps Pipelines.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Azure DevOps account. If you don't have an Azure DevOps account,  [create one for free](https://azure.microsoft.com/en-us/products/devops/) before you begin.
- Azure DevOps project that has docker support.
- Azure account. If you don't have an Azure account, [create one for free](https://azure.microsoft.com/en-gb/free/) before you begin. 
- Snyk account. If you don't have a Snyk account,  [create one for free](https://app.snyk.io/login?cta=sign-up&loc=nav&page=homepage) before you begin.

## Add Snyk Extension to Azure DevOps

1. In the root of an Azure DevOps Organization, select **Organization Settings**.

    ![Azure DevOps Organization Settings](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsOrganisationSettings.png)

2. In the **General** section, select **Extensions**.

    ![Azure DevOps Organization Settings General Extensions Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsOrganisationSettingsGeneralExtensionsNavigation.png)

3. Select **Browse marketplace**.
    ![Azure DevOps Extensions Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsExtensionsNavigationBar.png)

4. Search for **Snyk**, and then select **Snyk Security Scan**.

    ![Azure DevOps Service Connection Search Snyk](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsServiceConnectionSearchSnyk.png)

5. Select **Get if free**, then select **Install**.

    ![Azure DevOps Snyk Extension Get It Free](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykExtensionGetItFree.png)

6. In the **Extensions** section, **Snyk** is now installed.

    ![Azure DevOps Snyk Extension Get It Free](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykExtensionInstalled.png)

## Create Azure Container Registry

1. Search for **Container Registry**.

    ![Azure Search for Container Registries](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureContainerRegistry/AzureSearchContainerRegistries.png)

2. Select **+ Create**.

    ![Azure Container Registries Navigation Bar Create Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureContainerRegistry/AzureContainerRegistriesNavigationBar.png)


3. Enter the following values to **Basics**, then select **Review + create**, and then select **Create**.

    | Parameter | Value |
    | --- | --- | 
    | Subscription | Your Azure Subscription | 
    | Resource group | Create new with a name of your choice  | 
    | Registry name | Chosen name for the registry  |
    | Location | North Europe |
    | SKU | Basic |

    ![Azure](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Azure/AzureContainerRegistry/AzureContainerRegistrySetup.png)


## Add Snyk Service Connection to Azure DevOps Project

1. From the root of an Azure DevOps Project, select **Project settings**.

    ![Azure DevOps Projects Settings Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsProjectSettings.png)

2. In the **Pipelines** section, select **Service connections**. 

    ![Azure DevOps Project Settings Service Connections Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsProjectSettingsServiceConnectionsNavigation.png)

3. Select **Create service connection**.

    ![Azure DevOps Project Create Service Connection](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsCreateServiceConnection.png)

4. Search for **snyk**, then select **Next**.

    ![Azure DevOps Service Connection Search Snyk](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsServiceConnectionSearchSnyk.png)

5. Enter the following values.

    | Parameter | Value |
    | --- | --- | 
    | Server URL | https://snyk.io/ | 
    | Snyk API Token | Your Snyk API Token (see below) |
    | Service connection name | Snyk | 
    | Grant access permission to all pipelines | True |

    > [!NOTE]   
    > To get the **Snyk API Token**, select **Personal API Token**, then select **click to show** the **Key**. Copy and paste this key into Azure DevOps Snyk Service Connection Setup **Personal API Token** box.

    ![Snyk Account API Token](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Snyk/SnykAccountAPIToken.png)


    ![Azure DevOps Snyk Service Connection Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykServiceConnectionSetup.png)


## Add Azure Container Registry Service Connection to Azure DevOps Project

1. Complete steps 1 and 2 in [Add Snyk Service Connection to Azure DevOps Project](#add-snyk-service-connection-to-azure-devops-project).
2. Select **New service connection**.

     ![Azure DevOps Service Connection Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsServiceConnectionNavigationBar.png)

3. Search for **docker**, then select **Docker Registry**.

     ![Azure DevOps Service Connection Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsCreateDockerServiceConnection.png)

4. Enter the following values.

    | Parameter | Value |
    | --- | --- | 
    | Registry type | Azure Container Registry | 
    | Authentication Type | Service Principal | 
    | Subscription | Your Azure Subscription | 
    | Azure container registry | [Azure Container Registry created earlier](#create-azure-container-registry)   | 
    | Service connection name | Chosen name for the service connection  |
    | Grant access permission to all pipelines | True |

    ![Azure DevOps Service Connection Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsAzureContainerRegistryServiceConnectionSetup.png)


## Add Azure DevOps Snyk Pipeline

1. In the root of an Azure DevOps Project, select **Repos**.

    ![Azure DevOps Repos Files Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesNavigation.png)

2. Whilst in the root of your repo, select **More actions**, then **+ New**, and then **File**. 

    ![Azure DevOps Repos Files Add New File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesAddNewFile.png)

3. In the Modal Popup, name the file **snyk-az-pipeline.yml**.

    ![Azure DevOps Repos Files Add New File Modal Popup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesAddNewFileModalPopup.png)

4. Add the following contents to the **snyk-az-pipeline.yml** file, replacing `{Your...}` with your values.

    ```
    trigger:
    - main

    pool:
      vmImage: ubuntu-latest

    variables:
      buildConfiguration: 'Release'

    jobs:
    - job: Build
      steps: 
      - task: DotNetCoreCLI@2
        continueOnError: false
        inputs:
          command: 'build'
          projects: '{YourProjectFolder}*/**.csproj'

    - job: Build_and_Push_Docker_Image
      dependsOn: Build
      displayName: Build & Deploy to Azure Registry
      pool:
        vmImage: ubuntu-latest
      steps:
      - checkout: self
      - task: Docker@2
        displayName: Build an image
        inputs:
          containerRegistry: '{YourAzureContainerRegistryConnectionSetupAbove}'
          repository: '{YourNewAzureContainerRegistryRepository}'
          command: 'build'
          Dockerfile: '{YourProjectFolder}/Dockerfile'
          buildContext: '.'
          tags: 'dev'

      - task: SnykSecurityScan@1
        displayName: Scan an image
        inputs:
          serviceConnectionEndpoint: 'Snyk'
          testType: 'container'
          dockerImageName: '{YourAzureContainerRegistryRepositoryUrl}/{YourAzureContainerRegistryRepository}:dev'  
          dockerfilePath: '{YourProjectFolder}/Dockerfile'
          monitorWhen: 'always'
          failOnIssues: true
      
      - task: Docker@2
        displayName: Push an image
        inputs:
          containerRegistry: '{YourAzureContainerRegistryConnectionSetupAbove}'
          repository: '{YourNewAzureContainerRegistryRepository}'
          command: 'push'
          tags: dev
    ```
5. In the top right, select **Commit**.

    ![Azure DevOps Repos Commit Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesCommitNavigation.png)

5. in the bottom right, select **Commit**.

    ![Azure DevOps Repos Commit Comment](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesCommitComment.png)

## Create and Run Azure DevOps Snyk Pipeline

1. From the root of an Azure DevOps Project, select **Pipelines**.

    ![Azure DevOps Pipelines Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNavigation.png)

2. Select **Create Pipeline**.

    ![Azure DevOps Pipelines Create Your First Pipeline](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesCreateYourFirstPipeline.png)

3. Select **Azure Repos Git**.
    
    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesConnectAzureReposGit.png)

4. Select **Existing Azure Pipelines YAML file**.

    ![Azure DevOps Pipelines Configure Existing Azure Pipeline Yaml File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesConfigureExistingAzurePipelineYamlFile.png)

5. Enter the following values.

    | Parameter | Value |
    | --- | --- | 
    | Branch | main |
    | Path | /snyk-az-pipeline.yml |

    ![Azure DevOps Pipelines Select Existing Azure Pipeline Yaml File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSelectExistingAzurePipelineYamlFile.png)

6. Review the pipeline **snyk-az-pipeline.yml** yaml, and then select **Run**, to run the pipeline.

    ![Azure DevOps Pipelines Review Pipeline Yaml and Run Pipeline](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNewPipelineNavigationBar.png)

## View Snyk Test Report

1. Navigate to **Pipelines**.

    ![Azure DevOps Pipelines Snyk](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnyk.png)

3. In the **Runs** tab, select a pipeline that has been run. 

    ![Azure DevOps Pipelines Runs Pipelines Run](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesRunsPipeline.png)

4. Select the **Snyk Report** tab to view the Snyk test report.

    ![Azure DevOps Pipelines Snyk Test Report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnykReport.png)




