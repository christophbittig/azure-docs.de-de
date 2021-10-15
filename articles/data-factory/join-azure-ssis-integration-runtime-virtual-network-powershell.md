---
title: Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mithilfe von Azure PowerShell
description: Hier erfahren Sie, wie Sie eine Azure-SSIS Integration Runtime mithilfe von Azure PowerShell mit einem Azure Virtual Network verknüpfen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9d264bf4e52ec79a70d6efcd326f395685d12b2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403157"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-powershell"></a>Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mithilfe von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="define-the-variables"></a>Definieren der Variablen

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Sie müssen das virtuelle Netzwerk konfigurieren, bevor Sie die Azure-SSIS IR damit verknüpfen können. Um für die Azure-SSIS IR, die mit dem Netzwerk verknüpft werden soll, automatisch Berechtigungen und Einstellungen für virtuelle Netzwerke zu konfigurieren, fügen Sie das folgende Skript hinzu:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Erstellen einer Azure SSIS-IR und Verknüpfen dieser mit einem virtuellen Netzwerk

Sie können eine Azure SSIS-IR erstellen und gleichzeitig mit einem virtuellen Netzwerk verknüpfen. Das vollständige Skript und Anweisungen finden Sie unter [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime-powershell.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen einer Azure SSIS-Integration Runtime mit einem virtuellen Netzwerk

Im Artikel [Erstellen einer Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) wird veranschaulicht, wie im selben Skript eine Azure-SSIS IR erstellt und mit einem virtuellen Netzwerk verknüpft wird. Wenn Sie bereits über eine Azure-SSIS IR verfügen, führen Sie die folgenden Schritte aus, um sie mit dem virtuellen Netzwerk zu verknüpfen: 
1. Beenden Sie die Azure SSIS IR. 
1. Konfigurieren Sie die Azure SSIS-IR so, dass Sie mit dem virtuellen Netzwerk verknüpft wird. 
1. Starten Sie die Azure SSIS IR. 

## <a name="stop-the-azure-ssis-ir"></a>Beenden der Azure SSIS IR

Sie müssen die Azure-SSIS IR beenden, bevor Sie sie mit einem virtuellen Netzwerk verknüpfen können. Dieser Befehl gibt alle Knoten frei und beendet die Abrechnung:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurieren der Einstellungen des virtuellen Netzwerks für das Verknüpfen der Azure SSIS-IR

Verwenden Sie das folgende Skript, um Einstellungen für das virtuelle Netzwerk zu konfigurieren, mit dem die Azure-SSIS IR verknüpft werden soll: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="configure-the-azure-ssis-ir"></a>Konfigurieren der Azure SSIS IR

Um Ihre Azure-SSIS IR mit einem virtuellen Netzwerk zu verknüpfen, führen Sie den `Set-AzDataFactoryV2IntegrationRuntime`-Befehl aus: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

## <a name="start-the-azure-ssis-ir"></a>Starten der Azure SSIS IR

Führen Sie den folgenden Befehl aus, um die Azure-SSIS IR zu starten: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Das Abschließen dieses Befehls dauert 20 bis 30 Minuten.

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht: Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md)
- [Virtuelle Netzwerk-Details der Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über die Azure Data Factory Studio UI](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Dabei wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Sie erfahren, wie Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer SQL Managed Instance in einem virtuellen Netzwerk verwenden, um den SSIS-Katalog zu hosten. Außerdem wird veranschaulicht, wie Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen. Darüber hinaus enthält er Statusbeschreibungen für die zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Außerdem wird gezeigt, wie Sie Ihre Azure SSIS-IR aufskalieren, indem Sie weitere Knoten hinzufügen.
