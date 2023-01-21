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

In this article, you will learn how to scan a  container image in an Azure Container Registry with Snyk via Azure DevOps Pipelines.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Azure DevOps account. If you don't have a Azure DevOps account,  [create one for free](https://azure.microsoft.com/en-us/products/devops/) before you begin.
- Azure DevOps project.
- Azure Container Registry.
- Snyk account. If you don't have a Azure DevOps account,  [create one for free](https://app.snyk.io/login?cta=sign-up&loc=nav&page=homepage) before you begin.

## Add Snyk Extension to Azure DevOps

1. In the root of an Azure DevOps Organisation, select **Organisation Settings**
2. In the **General** section, select **Extensions**, then **Browse marketplace**
3. Search for **Snyk**, and then select **Snyk Security Scan**.
4. Select **Get if free**, then select **Install**
5. In the **Extensions** section, **Snyk** is now installed

## Create Azure Container Registry

1. Search for **Azure Container Registry**
2. Create
3. Complete form, Next to **Review + create**, select Create

## Add Snyk Service Connection to Azure DevOps Project

1. In the root of an Azure DevOps Project, select **Project settings**
2. In the **Pipelines** section, select **Service connection**, and then **Create service connection**
3. Search for **Synk** then, select **Next**
4. Complete the form with the following values.
> [!NOTE]   
> For Snyk API Token select **Personal API Token**, copy and paste the **Key** from the Account sections it into Azure DevOps **Snyk API Token**.

| ? | ? | ? | ? |
| --- | --- | --- | --- | 
| ? | ? | ? | ? |


## Add Azure Container Registry Service Connection to Azure DevOps Project

1. Complete steps 1 and 2 above.
2. Search for 

## Add Azure DevOps Pipeline

1. In the Azure Repo add a new yaml file named **snyk-az-pipeline.yml**
2. Add the following yaml, replacing `{Your...}`

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