---
title: 'Schnellstart: Erstellen eines Synapse-Arbeitsbereichs mithilfe von Azure PowerShell'
description: Erstellen Sie anhand der Schritte in diesem Leitfaden mithilfe von Azure PowerShell einen Azure Synapse-Arbeitsbereich.
services: synapse-analytics
author: rothja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: jroth
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee02f650ad20eb9f6673875823090ea3d242b7cd
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093001"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Schnellstart: Erstellen eines Azure Synapse-Arbeitsbereichs mit Azure PowerShell

Azure PowerShell ist ein Satz von Cmdlets für das direkte Verwalten von Azure-Ressourcen über PowerShell. Sie können sie in Ihrem Browser mit Azure Cloud Shell verwenden. Sie können sie auch unter macOS, Linux oder Windows installieren.

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell einen Synapse-Arbeitsbereich erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure Data Lake Storage Gen2-Speicherkonto](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Der Azure Synapse-Arbeitsbereich muss Lese- und Schreibvorgänge im ausgewählten ADLS Gen2-Konto ausführen können. Sie müssen für jedes Speicherkonto, das Sie als primäres Speicherkonto verknüpfen, bei der Erstellung des Speicherkontos den **hierarchischen Namespace** aktivieren. Die Vorgehensweise ist unter [Speicherkonto erstellen](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account) beschrieben.

Wenn Sie sich für die Verwendung von Cloud Shell entscheiden, finden Sie unter [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md) weitere Informationen.

### <a name="install-the-azure-powershell-module-locally"></a>Lokales Installieren des Azure PowerShell-Moduls

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

Weitere Informationen zur Authentifizierung mit Azure PowerShell finden Sie unter [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Installieren des Azure Synapse-PowerShell-Moduls

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.Synapse** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Erstellen eines Azure Synapse-Arbeitsbereichs mithilfe von Azure PowerShell

1. Definieren Sie erforderliche Umgebungsvariablen zum Erstellen von Ressourcen für den Azure Synapse-Arbeitsbereich.

   |        Variablenname        |                                                 BESCHREIBUNG                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Name des vorhandenen ADLS Gen2-Speicherkontos                                                           |
   | StorageAccountResourceGroup | Name der Ressourcengruppe des vorhandenen ADLS Gen2-Speicherkontos                                             |
   | FileShareName               | Name des vorhandenen Speicherdateisystems                                                                  |
   | SynapseResourceGroup        | Wählen Sie einen neuen Namen für Ihre Azure Synapse-Ressourcengruppe aus.                                                    |
   | Region                      | Wählen Sie eine der [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/geographies/#overview) aus. |
   | SynapseWorkspaceName        | Wählen Sie einen eindeutigen Namen für den neuen Azure Synapse-Arbeitsbereich aus.                                                  |
   | SqlUser                     | Wählen Sie einen Wert für einen neuen Benutzernamen aus.                                                                          |
   | SqlPassword                 | Wählen Sie ein sicheres Kennwort aus.                                                                                   |
   | ClientIP                    | Öffentliche IP-Adresse des Systems, über das Sie PowerShell ausführen                                             |
   |                             |                                                                                                             |

1. Erstellen Sie eine Ressourcengruppe als Container für Ihren Azure Synapse-Arbeitsbereich:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Erstellen Sie einen Azure Synapse-Arbeitsbereich:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Rufen Sie die Web- und Entwicklungs-URL für den Azure Synapse-Arbeitsbereich ab:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.dev
   ```

1. Erstellen Sie eine Firewallregel, um auf Ihrem Computer den Zugriff auf den Azure Synapse-Arbeitsbereich zuzulassen:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Öffnen Sie die Web-URL-Adresse für den Azure Synapse-Arbeitsbereich, die in der Umgebungsvariablen `WorkspaceWeb` gespeichert ist, um auf den Arbeitsbereich zuzugreifen:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse-Arbeitsbereich (Web)](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, um den Azure Synapse-Arbeitsbereich zu löschen.

> [!WARNING]
> Wenn Sie einen Azure Synapse-Arbeitsbereich löschen, werden die Analyse-Engines und die Daten, die in der Datenbank der enthaltenen SQL-Pools gespeichert sind, sowie die Arbeitsbereichsmetadaten entfernt. Es kann keine Verbindung mehr mit den SQL- oder Apache Spark-Endpunkten hergestellt werden. Alle Codeartefakte werden gelöscht (Abfragen, Notebooks, Auftragsdefinitionen und Pipelines). Das Löschen des Arbeitsbereichs wirkt sich **nicht** auf die Daten in der Data Lake Store Gen2-Instanz aus, die mit dem Arbeitsbereich verknüpft ist.

Wenn der in diesem Artikel erstellte Azure Synapse-Arbeitsbereich nicht benötigt wird, können Sie ihn löschen, indem Sie das folgende Beispiel ausführen:

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie [SQL-Pools](quickstart-create-sql-pool-studio.md) oder [Apache Spark-Pools](quickstart-create-apache-spark-pool-studio.md) erstellen, um mit der Analyse und Untersuchung Ihrer Daten zu beginnen.
