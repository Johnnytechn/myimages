---
title: Quickstart - Create an Azure Stream Analytics job by Azure Resource Manager template
description: This quickstart shows how to use the Azure Resource Manager template to create an Azure Stream Analytics job.
services: stream-analytics
ms.service: stream-analytics
author: Johnnytechn
ms.author: v-johya
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurepowershell, mode-arm
ms.date: 01/05/2023
---

# Quickstart: Create an Azure Stream Analytics job by using an ARM template

In this quickstart, you use an Azure Resource Manager template (ARM template) to create an Azure Stream Analytics job. Once the job is created, you validate the deployment.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal.

[![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.streamanalytics%2Fstreamanalytics-create%2Fazuredeploy.json)

## Prerequisites

To complete this article, you need to:

* Have an Azure subscription - [create one for trial](https://www.microsoft.com/china/azure/index.html?fromtype=cn).

## Review the template

The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/streamanalytics-create/).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "_generator": {
      "name": "bicep",
      "version": "0.8.9.13224",
      "templateHash": "10972314977021709659"
    }
  },
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    },
    "streamAnalyticsJobName": {
      "type": "string",
      "maxLength": 63,
      "minLength": 3,
      "metadata": {
        "description": "Stream Analytics Job Name, can contain alphanumeric characters and hypen and must be 3-63 characters long"
      }
    },
    "numberOfStreamingUnits": {
      "type": "int",
      "allowedValues": [
        1,
        3,
        6,
        12,
        18,
        24,
        30,
        36,
        42,
        48
      ],
      "maxValue": 48,
      "minValue": 1,
      "metadata": {
        "description": "Number of Streaming Units"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.StreamAnalytics/streamingjobs",
      "apiVersion": "2021-10-01-preview",
      "name": "[parameters('streamAnalyticsJobName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "Standard"
        },
        "outputErrorPolicy": "Stop",
        "eventsOutOfOrderPolicy": "Adjust",
        "eventsOutOfOrderMaxDelayInSeconds": 0,
        "eventsLateArrivalMaxDelayInSeconds": 5,
        "dataLocale": "en-US",
        "transformation": {
          "name": "Transformation",
          "properties": {
            "streamingUnits": "[parameters('numberOfStreamingUnits')]",
            "query": "SELECT\r\n    *\r\nINTO\r\n    [YourOutputAlias]\r\nFROM\r\n    [YourInputAlias]"
          }
        }
      }
    }
  ]
}
```

The Azure resource defined in the template is [Microsoft.StreamAnalytics/StreamingJobs](https://learn.microsoft.com/azure/templates/microsoft.streamanalytics/streamingjobs): create an Azure Stream Analytics job.

## Deploy the template

In this section, you create an Azure Stream Analytics job using the ARM template.

1. Select the following image to sign in to Azure and open a template. The template creates an Azure Stream Analytics job.

   [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.streamanalytics%2Fstreamanalytics-create%2Fazuredeploy.json)

2. Provide the required values to create your Azure Stream Analytics job.

   ![Create Azure Stream Analytics job using an Azure Resource Manager template](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Create Azure Stream Analytics job using an Azure Resource Manager template")

   Provide the following values:

   |Property  |Description  |
   |---------|---------|
   |**Subscription**     | From the drop-down, select your Azure subscription.        |
   |**Resource group**     | Specify whether you want to create a new resource group or use an existing one. A resource group is a container that holds related resources for an Azure solution. For more information, see [Azure Resource Group overview](../azure-resource-manager/management/overview.md). |
   |**Region**     | Select **China North**. For other available regions, see [Azure services available by region](https://azure.microsoft.com/regions/services/).        |
   |**Stream Analytics Job Name**     | Provide a name for your Stream Analytics job.      |
   |**Number of Streaming Units**     |  Choose the number of streaming units you need. For more information, see [Understand and adjust Streaming Units](stream-analytics-streaming-unit-consumption.md).       |

3. Select **Review + Create**, then **Create**.

## Review deployed resources

You can either use the Azure portal to check the Azure Stream Analytics job or use the following Azure CLI or Azure PowerShell script to list the resource.

### Azure CLI

```azurecli
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### Azure PowerShell

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## Clean up resources

If you plan to continue on to subsequent tutorials, you may wish to leave these resources in place. When no longer needed, delete the resource group, which deletes the Azure Stream Analytics job. To delete the resource group by using Azure CLI or Azure PowerShell:

### Azure CLI

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### Azure PowerShell

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

<!--Not available in MC: resource-manager-export.md-->

