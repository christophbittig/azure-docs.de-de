---
title: Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über Azure PowerShell
description: Erfahren Sie, wie Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über Azure PowerShell verknüpfen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a4d06256407fd42bdfa9f92cc2306df7dd33983c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087854"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-powershell"></a>Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre vorhandene Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF) über Azure PowerShell mit einem virtuellen Netzwerk verknüpfen. 

## <a name="create-variables"></a>Erstellen von Variablen

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Azure Resource Manager or Classic
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server configured with a private endpoint/IP firewall rule/virtual network service endpoint or Azure SQL Managed Instance that joins a virtual network to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend Azure Resource Manager virtual network, because classic virtual network will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for Azure SQL Database server configured with a virtual network service endpoint or a different subnet from the one used for Azure SQL Managed Instance that joins a virtual network
$SubnetId = $VnetId + '/subnets/' + $SubnetName 
# Virtual network injection method: Standard or Express. For comparison, see https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-virtual-network-configuration.
$VnetInjectionMethod = "Standard" # Standard by default, whereas Express lets you use the express virtual network injection method
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Sie müssen das virtuelle Netzwerk konfigurieren, bevor Sie die Azure-SSIS IR damit verknüpfen können. Um für die Azure-SSIS IR, die mit einem virtuellen Netzwerk verknüpft werden soll, automatisch Berechtigungen und Einstellungen für virtuelle Netzwerke zu konfigurieren, fügen Sie das folgende Skript hinzu:

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

Im Artikel [Erstellen einer Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime-powershell.md) wird veranschaulicht, wie im selben Skript eine Azure-SSIS IR erstellt und mit einem virtuellen Netzwerk verknüpft wird. Wenn Sie bereits über eine Azure-SSIS IR verfügen, führen Sie die folgenden Schritte aus, um sie mit einem virtuellen Netzwerk zu verknüpfen: 
1. Beenden Sie Ihre Azure-SSIS IR. 
1. Konfigurieren Sie Ihre Azure-SSIS IR für die Verknüpfung mit einem virtuellen Netzwerk. 
1. Starten Sie Ihre Azure-SSIS IR. 

## <a name="stop-your-azure-ssis-ir"></a>Beenden Ihrer Azure-SSIS IR

Sie müssen Ihre Azure-SSIS IR beenden, damit Sie sie mit einem virtuellen Netzwerk verknüpfen können. Dieser Befehl gibt alle Knoten frei und beendet die Abrechnung:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-your-azure-ssis-ir-to-join-a-virtual-network"></a>Konfigurieren Ihrer Azure-SSIS IR für die Verknüpfung mit einem virtuellen Netzwerk

Um Ihre Azure-SSIS IR mit einem virtuellen Netzwerk zu verknüpfen, führen Sie den `Set-AzDataFactoryV2IntegrationRuntime`-Befehl aus: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -SubnetId $SubnetId `
    -VNetInjectionMethod $VnetInjectionMethod

# Add public IP address parameters if you use the standard virtual network injection method and bring your own static public IP addresses
if($VnetInjectionMethod -eq "Standard")
{
    if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
    {
        $publicIPs = @($FirstPublicIP, $SecondPublicIP)
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -PublicIPs $publicIPs
    }
}
```

## <a name="start-your-azure-ssis-ir"></a>Starten Ihrer Azure-SSIS IR

Führen Sie den folgenden Befehl aus, um Ihre Azure-SSIS IR zu starten: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Wenn Sie die Express- oder Standard-Einschleusungsmethode für virtuelle Netzwerke verwenden, dauert die Durchführung dieses Befehls 5 bzw. 20 bis 30 Minuten.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines virtuellen Netzwerks zum Einschleusen einer Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express-Einschleusungsmethode für virtuelle Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Standard-Einschleusungsmethode für virtuelle Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Verknüpfen einer Azure-SSIS IR mit einem virtuellen Netzwerk über die ADF-Benutzeroberfläche](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Es wird der Azure-SQL-Datenbank-Server zum Hosten von SSISDB verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Er enthält Anweisungen zur Verwendung des Azure-SQL-Datenbank-Servers, der mit einem VNET-Dienstendpunkt/einer IP-Firewallregel/einem privaten Endpunkt konfiguriert ist, oder einer Azure SQL verwalteten Instanz, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verknüpft wird. Außerdem wird veranschaulicht, wie Sie Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
