---
title: Scan a Git Repo in an Azure Pipeline with SonarCloud
author: Peter Gregg
description: Scan a Git Repo in an Azure Pipeline with SonarCloud
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/03/08 07:40:00
categories: 
  - Azure DevOps
  - Azure Pipelines
  - SonarCloud
  - Security Vulnerabilities
---

# Scan Code with SonarCloud

In this article, you will learn how to scan a Git repo in an Azure Pipeline with SonarCloud.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Azure DevOps account. If you don't have an Azure DevOps account,  [create one for free](https://azure.microsoft.com/en-us/products/devops/) before you begin.
- Azure DevOps project, with a Git repository.
- SonarCloud account. If you don't have a SonarCloud account,  [create one for free with your Azure DevOps account](https://www.sonarsource.com/products/sonarcloud/signup/) before you begin.

## Add SonarCloud Extension to Azure DevOps

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
