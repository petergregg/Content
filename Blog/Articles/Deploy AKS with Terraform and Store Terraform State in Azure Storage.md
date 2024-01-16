---
title: Deploy AKS with Terraform and Store Terraform State in Azure Storage
author: Peter Gregg
description: Deploy Azure Kubernetes Service with Terraform and Store Terraform State in Azure Storage
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: published
published: 2024/01/16 18:50:00
categories: 
  - Azure
  - Azure Kubernetes Service (AKS)
  - Terraform
  - Azure CLI
  - Azure Storage
  - Powershell
  - Visual Studio Code 
---

# Deploy Azure Kubernetes Service with Terraform and Store Terraform State in Azure Storage

In this article, you will learn how to deploy Azure Kubernetes Service (AKS) with Terraform and store Terraform State in Azure Storage.

## Prerequisites
The following prerequisites will be required to complete this tutorial:
- Visual Studio Code. If you don't have Visual Studio Code installed, [download Visual Studio Code for free](https://code.visualstudio.com/Download) before you begin.
- Terraform Installed. If you don't have Terraform installed, [download Terraform for free](https://developer.hashicorp.com/terraform/tutorials/azure-get-started/install-cli) before you begin.
- Azure CLI Installed. If you don't have Azure CLI installed, [download Azure CLI for free](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- Azure account. If you don't have an Azure account, [create one for free](https://azure.microsoft.com/en-gb/free/) before you begin.

# Authenticate Terraform using Azure CLI

1. Open **Visual Studio Code**, select **Terminal**, and then **New Terminal**. Ensure you are running a Powershell terminal.
    ![Visual Studio Code Open New Terminal](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/VisualStudioCode/VisualStudioCodeOpenTerminal.png)

2. Run the following command in the Terminal, to log in to Azure.

    ```
    az login
    ```

3. Your browser will open and prompt you to enter your **Azure login credentials**. Once authenticated, your terminal will display your azure subscriptions.

4. Copy the **id** of subscription you would like to use.

5. Run the following command in the **Terminal**, replacing `{YourAzureSubscriptionId}` with your azure subscription id.

    ```
    az account set --subscription "{YourAzureSubscriptionId}"
    ```

# Create an Azure Service Principal

1. Run the following command in the **Terminal** to create an **Azure Service Principal**, replacing `{YourAzureSubscriptionId}` with your azure subscription id.

    ```
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/{YourAzureSubscriptionId}"
    ```

2. Run the following powershell commands in the **Terminal**, replacing `{Your...}` with your details, to add the details to environment variables. 
    ```
    $env:TF_VAR_CLIENT_ID = "{YourServicePrincipalClientId}"
    $env:TF_VAR_CLIENT_SECRET = "{YourServicePrincipalClientSecret}"
    $env:TF_VAR_TENANT_ID = "{YourTenantId}"
    $env:TF_VAR_SUBSCRIPTION_ID = "{YourSubscriptionId}"
    $env:TF_VAR_PRINCIPAL_ID = "{YourServicePrincipalObjectId}"
    ```

    !NOTE: Environment variables that are named using the pattern `TF_VAR_` can be directly accessed via Terraform config.

# Create an Azure Storage Account with Terraform for Remote State

1. Create a new folder in **Visual Studio Code**, named `tf-state`. Add a file named `main.tf` into the `tf-state` folder and paste the following Terraform configuration into it.

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

2. Add a file named `variables.tf` into the `tf-state` folder and paste the following terraform configuration into it.

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

3. Right click on the `tf-state` folder and select **Open in Integrated Terminal**.
4. Run `terraform init` in the terminal to initialize the Terraform configuration.
5. Run `terraform fmt` in the terminal to format and validate the Terraform configuration.
6. Run `terraform plan` in the terminal to see any planned changes.
7. Run `terraform apply` in the terminal to apply the Terraform configuration. Navigate to the [Azure portal](https://portal.azure.com/) in your web browser to validate the azure storage account.

# Create AKS Terraform configuration

1. In **Visual Studio Code**, create a new folder in the root named `aks`. 

2. Add a file named `variables.tf` into the `aks` folder and paste the following Terraform configuration into it.

    ```
    variable "CLIENT_ID" {
      description = "Azure TF Service Principal Client Id"
    }
    
    variable "CLIENT_SECRET" {
      description = "Azure TF Service Principal Password"
    }
    ```

3. Add a file named `main.tf` into the `aks` folder and paste the following Terraform configuration into it.
   
    ```
    resource "random_pet" "prefix" {}
    
    provider "azurerm" {
      features {}
    }
    
    resource "azurerm_resource_group" "default" {
      name     = "${random_pet.prefix.id}-rg"
      location = "West Europe"
    
      tags = {
        environment = "staging"
      }
    }
    
    resource "azurerm_kubernetes_cluster" "default" {
      name                = "${random_pet.prefix.id}-aks"
      location            = azurerm_resource_group.default.location
      resource_group_name = azurerm_resource_group.default.name
      dns_prefix          = "${random_pet.prefix.id}-k8s"
    
      default_node_pool {
        name            = "default"
        node_count      = 2
        vm_size         = "Standard_B2s"
        os_disk_size_gb = 30
      }
    
      service_principal {
        client_id     = var.CLIENT_ID
        client_secret = var.CLIENT_SECRET
      }
    
      role_based_access_control {
        enabled = true
      }
    
      tags = {
        environment = "staging"
      }
    }
    ```

4. Add a file named `outputs.tf` into the `aks` folder and paste the following Terraform configuration into it.

    ```
    output "resource_group_name" {
      value = azurerm_resource_group.default.name
    }
    
    output "kubernetes_cluster_name" {
      value = azurerm_kubernetes_cluster.default.name
    }
    ```

5. Add a file named `providers.tf` into the `aks` folder and paste the following Terraform configuration into it.

    ```
    terraform {
      required_providers {
        azurerm = {
          source  = "hashicorp/azurerm"
          version = "2.66.0"
        }
      }
    
      required_version = ">= 0.14"
    }
    ```

## Configure Terraform Backend State

1. Add the following backend Terraform configuration into the `providers.tf`, replacing `{YourTFStateAzureStorageAccount}` with the Terraform state storage account you created earlier and `{YourAzureSubscription}` with your azure subscription id.

    ```
    terraform {
      required_providers {
        ...
      }
    
      backend "azurerm" {
          resource_group_name  = "tfstate"
          storage_account_name = "{YourTFStateAzureStorageAccount}"
          container_name       = "tfstate"
          key                  = "terraform.tfstate"
          subscription_id      = "{YourAzureSubscription}"
      }
    
    required_version = ">= 0.14"
    ```

  2. Navigate to the [Azure portal](https://portal.azure.com/) in your web browser to validate the AKS.

# Delete Azure Resources

1. Right click on the `aks` folder and select **Open in Integrated Terminal**.
2. Run `terraform destroy` in the terminal to destroy the AKS resources.
3. Right click on the `tf-state` folder and select **Open in Integrated Terminal**.
4. Run `terraform destroy` in the terminal to destroy the Azure Storage resources.












