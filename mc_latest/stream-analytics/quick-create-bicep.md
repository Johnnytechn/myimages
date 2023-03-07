---
title: Quickstart - Create an Azure Stream Analytics job using Bicep
description: This quickstart shows how to use Bicep to create an Azure Stream Analytics job.
ms.service: stream-analytics
author: Johnnytechn
ms.author: v-johya
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurepowershell, mode-arm
ms.date: 11/08/2022
---

# Quickstart: Create an Azure Stream Analytics job using Bicep

In this quickstart, you use Bicep to create an Azure Stream Analytics job. Once the job is created, you validate the deployment.

[!INCLUDE [About Bicep](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## Prerequisites

To complete this article, you need to have an Azure subscription. [Create one for trial](https://www.microsoft.com/china/azure/index.html).

## Review the Bicep file

The Bicep file used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/streamanalytics-create/).

```bicep
@description('Location for the resources.')
param location string = resourceGroup().location

@description('Stream Analytics Job Name, can contain alphanumeric characters and hypen and must be 3-63 characters long')
@minLength(3)
@maxLength(63)
param streamAnalyticsJobName string

@description('Number of Streaming Units')
@minValue(1)
@maxValue(48)
@allowed([
  1
  3
  6
  12
  18
  24
  30
  36
  42
  48
])
param numberOfStreamingUnits int

resource streamingJob 'Microsoft.StreamAnalytics/streamingjobs@2021-10-01-preview' = {
  name: streamAnalyticsJobName
  location: location
  properties: {
    sku: {
      name: 'Standard'
    }
    outputErrorPolicy: 'Stop'
    eventsOutOfOrderPolicy: 'Adjust'
    eventsOutOfOrderMaxDelayInSeconds: 0
    eventsLateArrivalMaxDelayInSeconds: 5
    dataLocale: 'en-US'
    transformation: {
      name: 'Transformation'
      properties: {
        streamingUnits: numberOfStreamingUnits
        query: 'SELECT\r\n    *\r\nINTO\r\n    [YourOutputAlias]\r\nFROM\r\n    [YourInputAlias]'
      }
    }
  }
}
```

The Azure resource defined in the Bicep file is [Microsoft.StreamAnalytics/StreamingJobs](https://learn.microsoft.com/azure/templates/microsoft.streamanalytics/streamingjobs): create an Azure Stream Analytics job.

## Deploy the Bicep file

1. Save the Bicep file as **main.bicep** to your local computer.
1. Deploy the Bicep file using either Azure CLI or Azure PowerShell.

    # [CLI](#tab/CLI)

    ```azurecli
    az group create --name exampleRG --location chinanorth
    az deployment group create --resource-group exampleRG --template-file main.bicep --parameters streamAnalyticsJobName =<job-name> numberOfStreamingUnits=<int>
    ```

    # [PowerShell](#tab/PowerShell)

    ```azurepowershell
    New-AzResourceGroup -Name exampleRG -Location chinanorth
    New-AzResourceGroupDeployment -ResourceGroupName exampleRG -TemplateFile ./main.bicep -streamAnalyticsJobName "<job-name>" -numberOfStreamingUnits <int>
    ```

    ---

    You need to provide values for the following parameters:

    - **streamAnalyticsJobName**: Replace **\<job-name\>** with the Stream Analytics job name. The name can contain alphanumeric characters and hyphens, and it must be at least 3-63 characters long.
    - **numberOfStreamingUnits**: Replace **\<int\>** with the number of Streaming Units. Allowed values include: 1, 3, 6, 12, 18, 24, 30, 36, 42, and 48.

    > [!NOTE]
    > When the deployment finishes, you should see a message indicating the deployment succeeded.

## Review deployed resources

You can either use the Azure portal to check the Azure Stream Analytics job or use the following Azure CLI or Azure PowerShell script to list the resource.

### Azure CLI

Use the Azure portal, Azure CLI, or Azure PowerShell to list the deployed resources in the resource group.

# [CLI](#tab/CLI)

```azurecli
az resource list --resource-group exampleRG
```

# [PowerShell](#tab/PowerShell)

```azurepowershell
Get-AzResource -ResourceGroupName exampleRG
```

---

## Clean up resources

If you plan to continue on to subsequent tutorials, you may wish to leave these resources in place. When no longer needed, delete the resource group, which deletes the Azure Stream Analytics job. To delete the resource group by using Azure CLI or Azure PowerShell:

# [CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# [PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## Next steps

In this quickstart, you created an Azure Stream Analytics job using Bicep and validated the deployment. To learn how to create your own Bicep files using Visual Studio Code, continue on to the following article:

> [!div class="nextstepaction"]
> [Quickstart: Create Bicep files with Visual Studio Code](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)

