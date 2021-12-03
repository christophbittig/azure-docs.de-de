---
title: Verwenden einer Azure Resource Manager-Vorlage zum Erstellen einer Integration Runtime
description: Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage zum Erstellen einer Azure-SSIS-Integration Runtime in Azure Data Factory verwenden, damit Sie SSIS-Pakete in Azure bereitstellen und ausführen können.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 322a2277169b53a9c49d496524789636a709af2a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852634"
---
# <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Verwenden einer Azure Resource Manager-Vorlage zum Erstellen einer Integration Runtime

In diesem Abschnitt verwenden Sie eine Azure Resource Manager-Vorlage, um die Azure-SSIS Integration Runtime zu erstellen. 

## <a name="sample-azure-resource-manager-template"></a>Beispiel für eine Azure Resource Manager-Vorlage

Die Erstellung einer Azure-SSIS Integration Runtime mit einer Azure Resource Manager-Vorlage umfasst folgende Schritte:

1. Erstellen Sie eine JSON-Datei mit der folgenden Azure Resource Manager-Vorlage. Ersetzen Sie die Werte in spitzen Klammern (Platzhalter) durch Ihre eigenen Werte.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Führen Sie den Befehl `New-AzResourceGroupDeployment` wie im folgenden Beispiel aus, um die Azure Resource Manager-Vorlage bereitzustellen. Im Beispiel ist `ADFTutorialResourceGroup` der Name Ihrer Ressourcengruppe. `ADFTutorialARM.json` ist die Datei, die die JSON-Definition für Ihre Data Factory und die Azure-SSIS IR enthält.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Mit diesem Befehl wird die Data Factory und darin eine Azure-SSIS IR erstellt, aber er dient nicht zum Starten der IR.

3. Führen Sie den Befehl `Start-AzDataFactoryV2IntegrationRuntime` aus, um Ihre Azure-SSIS IR zu starten:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von fünf Minuten abgeschlossen sein. Es kann aber 20 bis 30 Minuten dauern, bis der Betritt einer Azure-SSIS IR zu einem virtuellen Netzwerk abgeschlossen ist.
>
> Bei Verwendung der SSISDB stellt der Data Factory-Dienst eine Verbindung mit Ihrem Datenbankserver her, um die SSISDB vorzubereiten. Außerdem konfiguriert er Berechtigungen und Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die Azure-SSIS IR mit dem virtuellen Netzwerk.
> 
> Wenn Sie Azure-SSIS IR bereitstellen, werden auch die Access Redistributable-Komponente und das Azure Feature Pack für SSIS installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-Dateien, Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten bereits unterstützt werden. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Weitere Informationen zu zusätzlichen Komponenten, die Sie installieren können, finden Sie unter [Custom setups for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Setups for Azure-SSIS IR).

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe des Azure-Portals bereitstellen](create-azure-ssis-integration-runtime-portal.md).
- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe von Azure PowerShell bereitstellen](create-azure-ssis-integration-runtime-powershell.md).
- [Stellen Sie Ihre SSIS-Pakete in Azure Data Factory bereit, und führen Sie diese aus](create-azure-ssis-integration-runtime-deploy-packages.md).

Sehen Sie sich auch andere Themen zur Azure-SSIS IR in dieser Dokumentation an:

- [Azure SSIS-Integration Runtime:](concepts-integration-runtime.md#azure-ssis-integration-runtime) Dieser Artikel enthält allgemeine Informationen zu Integration Runtimes, einschließlich der Azure-SSIS IR.
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
- [Bereitstellen und Ausführen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung) 
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
