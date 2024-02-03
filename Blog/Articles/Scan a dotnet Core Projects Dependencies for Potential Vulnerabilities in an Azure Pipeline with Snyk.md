---
title: Scan a dotnet Core Projects Dependencies for Potential Vulnerabilities in an Azure Pipeline with Snyk
author: Peter Gregg and ChatGPT
description: Scan a dotnet Core Projects Dependencies for Potential Vulnerabilities in an Azure Pipeline with Snyk
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2024/02/03 13:08:00
categories: 
  - Azure DevOps
  - Azure Pipelines
  - Snyk
  - Git
  - Security
  - dotnet Core
---

# Securing Your Dependencies: Integrating Snyk into Azure Pipelines

In the dynamic landscape of modern software development, security is not just a buzzword—it's a prerequisite. Open-source dependencies inject agility into our projects but also introduce potential vulnerabilities. Snyk, is a developer-centric solution for identifying and mitigating these risks. In this article, we'll walk through seamlessly integrating Snyk into Azure Pipelines, ensuring your dependencies are as robust as your code.

## Understanding the Imperative

Before diving into the integration, let's acknowledge why managing dependencies is paramount. Our applications lean heavily on third-party libraries, but these can be an Achilles' heels if not managed meticulously. Snyk steps in as the guardian, offering a proactive stance against vulnerabilities, allowing developers to navigate the intricate web of dependencies securely.

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Azure DevOps account. If you don't have an Azure DevOps account,  [create one for free](https://azure.microsoft.com/en-us/products/devops/) before you begin.
- Azure DevOps project, with a dotnet core project in a Git repository. If you don't have a repo [fork the articles Git repo](https://twogsdev.visualstudio.com/_git/CoreDeployTest).
- Snyk account. If you don't have a Snyk account,  [create one for free with your Azure DevOps account](https://app.snyk.io/login/?_gl=1%2a14oxqhz%2a_ga%2aOTQ4MDkwMjEwLjE3MDY5NDcxMzE.%2a_ga_X9SH3KP7B4%2aMTcwNjk1NTAzNC4yLjEuMTcwNjk1NTA1My4wLjAuMA..) before you begin.

## Add Snyk Extension to Azure DevOps

Begin by embracing Snyk into your Azure Pipelines workflow. The process, akin to the simplicity synonymous with Azure, empowers developers to fortify their projects effortlessly.

1. In the root of an Azure DevOps Organization, select **Organization Settings** in the bottom left of the screen.

    ![Azure DevOps Organization Settings](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsOrganisationSettings.png)

2. In the **General** section, select **Extensions**.

    ![Azure DevOps Organization Settings General Extensions Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsOrganisationSettingsGeneralExtensionsNavigation.png)

3. Select **Browse marketplace**.

    ![Azure DevOps Extensions Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsExtensionsNavigationBar.png)

4. Search for **snyk**, and then select **Snyk Security Scan**.

    ![Azure DevOps Extension Search Snyk](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykExtension.png)

5. Select **Get if free**, and then select **Install**.

    ![Azure DevOps Snyk Extension Get It Free](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykExtensionGetItFree.png)

6. In the **Extensions** section, **Snyk Security Scan** is now installed.

    ![Azure DevOps Snyk Extension Installed](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykExtensionInstalled.png)


## Add Snyk Service Connection to Azure DevOps Project

1. From the root of an Azure DevOps Project, select **Project settings** in the bottom left of the screen.

    ![Azure DevOps Project Settings Link](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsProjectSettings.png)

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

    ![Azure DevOps Snyk Service Connection Setup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsSnykServiceConnectionSetup.png)
 
    > To get the **Snyk API Token**, in [snyk account settings](https://app.snyk.io/account), select **General**, then select **click to show** the **Key**. Copy and paste the key into Azure DevOps Snyk Service Connection Setup **Personal API Token** box.
    ![Snyk Account API Token](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/Snyk/SnykAccountAPIToken.png)

### Create and Run Azure DevOps Snyk Pipeline

With the extension in place, incorporate the Snyk Scan task into your pipeline configuration. This task serves as the sentinel, scrutinizing your dependencies for vulnerabilities.

1. In the root of an Azure DevOps Project, select **Pipelines**.

    ![Azure DevOps Pipelines Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNavigation.png)

2. Select **Create Pipeline**.

    ![Azure DevOps Pipelines Create Your First Pipeline](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesCreateYourFirstPipeline.png)

3. In the **Connect** tab, Select **Azure Repos Git**.
    
    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesConnectAzureReposGit.png)

5. In the **Select** tab, select **ASP.NET Core** YAML template.

    ![Azure DevOps Pipelines Select ASP.NET Core YAML template](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNETCoreYamlTemplate.png)

6. Replace the Contents of the YAML file with the following YAML.

    ```yaml
    trigger:
    - master

    pool:
    vmImage: ubuntu-latest

    steps:
    - task: DotNetCoreCLI@2
        inputs:
        command: 'build'
    - task: SnykSecurityScan@1
      inputs:
        serviceConnectionEndpoint: 'Snyk'
        testType: 'app'
        failOnIssues: true
        monitorWhen: 'always'
        additionalArguments: '--all-projects --exclude=tests'
    ```  

7. Rename the pipelines YAML file to **snykappsecurityscan-az-pipeline.yml** and then select **Save and run**.

    ![Azure DevOps Pipelines Rename the Pipelines Yaml File, Save and Run](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnykAppSecurityScanSaveAndRunNavigation.png)

8. Add a commit message and then select **Save and run**. 

    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnykAppSecurityScanCommitSaveAndRun.png)

## Review Snyk Test Report and Remediate

As the Snyk scan concludes, dive into the results presented in your Azure Pipelines console. Armed with detailed insights and recommendations, developers can swiftly prioritize and address vulnerabilities—a testament to Snyk's commitment to actionable security.

1. Navigate to **Pipelines**.

    ![Azure DevOps Pipelines Snyk](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnykAppSecurityScan.png)

3. In the **Runs** tab, select a pipeline that has been run. 

    ![Azure DevOps Pipelines Runs Pipelines Run](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnykAppSecurityScanRunsPipeline.png)

4. Select the **Snyk Report** tab to view the Snyk test report.

    ![Azure DevOps Pipelines Snyk Test Report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesSnykAppSecurityScanReport.png)


# Conclusion

By seamlessly integrating Snyk into your Azure Pipelines workflow, you elevate your development process from merely functional to inherently secure. Snyk's commitment to developer-first security aligns seamlessly with the ethos of Azure Pipelines, providing a robust solution for managing and securing your dependencies. With Snyk as your vigilant ally, the intricate dance of dependencies becomes a secure and confident stride towards code excellence.