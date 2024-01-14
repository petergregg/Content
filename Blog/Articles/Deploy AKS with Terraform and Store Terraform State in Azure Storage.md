---
title: Deploy AKS with Terraform and Store Terraform State in Azure Storage
author: Peter Gregg
description: Deploy AKS with Terraform
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2024/01/14 14:00:00
categories: 
  - Azure
  - AKS
  - Terraform
  - Azure CLI
  - Azure Storage
---

# Deploy AKS with Terraform and Store Terraform State in Azure Storage

In this article, you will learn how to deploy AKS with Terraform and Store Terraform State in Azure Storage.

## Prerequisites
The following prerequisites will be required to complete this tutorial:
- Visual Studio Code. If you don't have Visual Studio Code installed, [download Visual Studio Code for free](https://code.visualstudio.com/Download) before you begin.
- Terraform Installed. If you don't have Terraform installed, [download Terraform for free](https://developer.hashicorp.com/terraform/tutorials/azure-get-started/install-cli) before you begin.
- Azure CLI Installed. If you don't have Azure CLI installed, [download Azure CLI for free](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- Azure account. If you don't have an Azure account, [create one for free](https://azure.microsoft.com/en-gb/free/) before you begin.

# Authenticate Terraform using Azure CLI

1. Open **Visual Studio Code**, select **Terminal**, and then **New Terminal**. Ensure you are running a Powershell terminal.
    ![Visual Studio Code Open New Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeOpenTerminal.png)

2. Run the following command in the Terminal.

    ```
    az login
    ```

3. Your browser will open and prompt you to enter your **Azure login credentials**. Once authenticated, your terminal will display your azure subscriptions.

4. Copy the **id** of subscription you would like to use.

5. Run the following command in the Terminal, replacing `{Your...}` with your subscription id.

    ```
    az account set --subscription "{YourSubscriptionId}"
    ```

# Create an Azure Service Principal

1. Run the following command to create a Service Principal, replacing `{Your...}` with your subscription id.

    ```
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/{YourSubscriptionId}"
    ```

2. Add the following to environment variables via the following powershell commands, replacing `{Your...}` with your credentials.
    ```
    $env:TF_VAR_CLIENT_ID = "{YourServicePrincipalClientId}"
    $env:TF_VAR_CLIENT_SECRET = "{YourServicePrincipalClientSecret}"
    $env:TF_VAR_TENANT_ID = "{YourTenantId}"
    $env:TF_VAR_SUBSCRIPTION_ID = "{YourSubscriptionId}"
    $env:TF_VAR_PRINCIPAL_ID = "{YourServicePrincipalObjectId}"
    ```

    !NOTE: Environment variables that are named using the pattern `TF_VAR_` can be directly accessed via terraform config.

# Create an Azure Storage Account with Terraform for remote state

1. Create a new project in visual studio code. Add a new folder named `tf-state`. Add a main.tf file with the following terraform configuration.

    ```
    terraform {
        required_providers {
            azurerm = {
            source  = "hashicorp/azurerm"
            version = "~>3.0"
            }
        }
    }

    provider "azurerm" {
        features {}
        client_id       = var.CLIENT_ID
        client_secret   = var.CLIENT_SECRET
        tenant_id       = var.TENANT_ID
        subscription_id = var.SUBSCRIPTION_ID
    }

    resource "random_string" "resource_code" {
        length  = 5
        special = false
        upper   = false
    }

    resource "azurerm_resource_group" "tfstate" {
        name     = "tfstate"
        location = "ukwest"
    }

    resource "azurerm_storage_account" "tfstate" {
        name                     = "tfstate${random_string.resource_code.result}"
        resource_group_name      = azurerm_resource_group.tfstate.name
        location                 = azurerm_resource_group.tfstate.location
        account_tier             = "Standard"
        account_replication_type = "LRS"
        allow_nested_items_to_be_public = false

        tags = {
            environment = "staging"
        }
    }

    resource "azurerm_storage_container" "tfstate" {
        name                  = "tfstate"
        storage_account_name  = azurerm_storage_account.tfstate.name
        container_access_type = "private"
    }

    # Grant the necessary permissions to the service principal on the storage account
    resource "azurerm_role_assignment" "tfstate" {
        scope                = azurerm_storage_account.tfstate.id
        role_definition_name = "Contributor"
        principal_id         = var.PRINCIPAL_ID
    }
    ```

2. Add a variables.tf file with the following terraform configuration.

    ```
    variable "CLIENT_ID {
        description = "Azure Terraform Service Principal Client Id"
    }

    variable "CLIENT_SECRET {
        description = "Azure Terraform Service Principal Password"
    }

    variable "TENANT_ID {
        description = "Azure Terraform Tenant Id"
    }

    variable "SUBSCRIPTION_ID {
        description = "Azure Terraform Subscription Id"
    }

    variable "PRINCIPAL_ID {
        description = "Azure Terraform Service Principal Id"
    }
    ```

3. Right click on the tf-state folder and select open in integrated terminal.
4. Run `terraform` init to ...
5. Run `terraform` plan to ...
6. Run `terraform` apply to ...
7. Run `terraform` destroy to ...

# Create AKS Terraform configuration

1. Create a new folder in the root and name it `aks`. 

2. Add a file named variables.tf and paste the below terraform configuration.

    ```

    ```


3. Add a file named main.tf and paste the below terraform configuration.
   
    ```

    ```

4. Add a file named outputs.tf and paste the below terraform configuration.

    ```

    ```

5. Add a new file name providers.tf and paste the below terraform configuration.

    ```

    ```

## Configure terraform backend State

1. Add the below configuration to providers.tf.

    ```

    ```











