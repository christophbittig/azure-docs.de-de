---
title: Verschieben eines Azure Synapse Analytics-Arbeitsbereichs aus einer Region in eine andere
description: In diesem Artikel erfahren Sie, wie Sie einen Azure Synapse Analytics-Arbeitsbereich aus einer Region in eine andere verschieben.
author: phanir
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 08/16/2021
ms.author: phanir
ms.reviewer: jrasnick
ms.openlocfilehash: 6ad246eefb86c31291d2a9745c6f77e276701744
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214083"
---
# <a name="move-an-azure-synapse-analytics-workspace-from-one-region-to-another"></a>Verschieben eines Azure Synapse Analytics-Arbeitsbereichs aus einer Region in eine andere

Dieser Artikel ist eine Schritt-für-Schritt-Anleitung, die zeigt, wie Sie einen Azure Synapse Analytics-Arbeitsbereich aus einer Azure-Region in eine andere verschieben.

> [!NOTE]
> Mit den Schritten in diesem Artikel wird der Arbeitsbereich nicht tatsächlich verschoben. Die Schritte zeigen Ihnen, wie Sie einen neuen Arbeitsbereich in einer neuen Region erstellen, indem Sie dedizierte SQL-Poolsicherungen und Artefakte von Azure Synapse Analytics aus der Quellregion verwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Integrieren Sie den Azure Synapse-Arbeitsbereich der Quellregion in Azure DevOps oder GitHub. Weitere Informationen finden Sie unter [Quellcodeverwaltung in Synapse Studio](cicd/source-control.md).
- Sorgen Sie dafür, dass [Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.3.0&preserve-view=true)- und [Azure CLI](/cli/azure/install-azure-cli)-Module auf dem Server installiert werden, auf dem Skripts ausgeführt werden.
- Stellen Sie sicher, dass alle abhängigen Dienste, z. B. Azure Machine Learning, Azure Storage und Azure Private Link-Hubs, in der Zielregion neu erstellt oder in die Zielregion verschoben werden, wenn der Dienst eine Regionsverschiebung unterstützt. 
- Verschieben von Azure Storage in eine andere Region. Weitere Informationen finden Sie unter [Verschieben eines Azure Speicherkontos in eine andere Region](../storage/common/storage-account-move.md).
- Stellen Sie sicher, dass der Name des dedizierten SQL-Pools und der Name des Apache Spark-Pools im Arbeitsbereich in der Quellregion und in dem in der Zielregion identisch sind.

## <a name="scenarios-for-a-region-move"></a>Szenarien für eine Regionsverschiebung

- **Neue Complianceanforderungen**: Organisationen verlangen im Rahmen neuer Complianceanforderungen, dass Daten und Dienste in derselben Region platziert werden.
- **Verfügbarkeit einer neuen Azure-Region**: Szenarien, in denen eine neue Azure-Region verfügbar ist und Projekt- oder Geschäftsanforderungen zum Verschieben des Arbeitsbereichs und anderer Azure-Ressourcen in die neu verfügbare Azure-Region vorliegen.
- **Falsche Region ausgewählt**: Die falsche Region wurde ausgewählt, als die Azure-Ressourcen erstellt wurden.

## <a name="steps-to-move-an-azure-synapse-workspace-to-another-region"></a>Schritte zum Verschieben eines Azure Synapse-Arbeitsbereichs in eine andere Region

Das Verschieben eines Azure Synapse-Arbeitsbereichs aus einer Region in eine andere ist ein mehrstufiger Prozess. Die allgemeinen Schritte sind folgende:

1. Erstellen Sie einen Azure Synapse-Arbeitsbereich in der Zielregion zusammen mit einem Spark-Pool mit denselben Konfigurationen wie im Arbeitsbereich der Quellregion.
1. Stellen Sie den dedizierten SQL-Pool mithilfe von Wiederherstellungspunkten oder Geosicherungen in der Zielregion wieder her.
1. Erstellen Sie alle erforderlichen Anmeldungen auf dem neuen logischen SQL Server neu.
1. Erstellen Sie serverlose SQL-Pool- und Spark-Pooldatenbanken und -Objekte.
1. Fügen Sie einen Azure DevOps-Dienstprinzipal zur Rolle „Synapse-Artefaktherausgeber“ der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Synapse hinzu, wenn Sie eine Azure DevOps-Releasepipeline zum Bereitstellen der Artefakte verwenden.
1. Stellen Sie Codeartefakte (SQL-Skripts, Notebooks), verknüpfte Dienste, Pipelines, Datasets, Trigger für Spark-Auftragsdefinitionen und Anmeldeinformationen aus Azure DevOps-Releasepipelines im Azure Synapse-Arbeitsbereich in der Zielregion bereit.
1. Fügen Sie Azure Active Directory-Benutzer oder -Gruppen (Azure AD) zu RBAC-Rollen von Azure Synapse hinzu. Gewähren Sie der systemseitig zugewiesenen verwalteten Identität (SA-MI) „Mitwirkender an Storage-Blob“-Zugriff auf Azure Storage und Azure Key Vault, wenn Sie sich mithilfe einer verwalteten Identität authentifizieren.
1. Erteilen Sie erforderlichen Azure AD-Benutzern die Rollen „Leser von Storage-Blob“ oder „Mitwirkender an Storage-Blob“ für angefügten Standardspeicher oder für das Speicherkonto, das Daten enthält, die mithilfe eines serverlosen SQL-Pools abgefragt werden sollen.
1. Erstellen Sie die selbstgehostete Integrationslaufzeit (SHIR) neu.
1. Laden Sie alle erforderlichen Bibliotheken und JAR-Dateien manuell in den Azure Synapse-Zielarbeitsbereich hoch.
1. Erstellen Sie alle verwalteten privaten Endpunkte, wenn der Arbeitsbereich in einem verwalteten virtuellen Netzwerk bereitgestellt wird.
1. Testen Sie den neuen Arbeitsbereich in der Zielregion, und aktualisieren Sie alle DNS-Einträge, die auf den Arbeitsbereich der Quellregion verweisen.
1. Wenn im Quellarbeitsbereich eine Verbindung mit einem privaten Endpunkt erstellt wurde, erstellen Sie eine im Arbeitsbereich der Zielregion.
1. Sie können den Arbeitsbereich in der Quellregion löschen, nachdem Sie ihn gründlich getestet haben, und alle Verbindungen an den Arbeitsbereich der Zielregion umleiten.
## <a name="prepare"></a>Vorbereiten
## <a name="step-1-create-an-azure-synapse-workspace-in-a-target-region"></a>Schritt 1: Erstellen eines Azure Synapse-Arbeitsbereichs in einer Zielregion

In diesem Abschnitt erstellen Sie den Azure Synapse-Arbeitsbereich mithilfe der Azure PowerShell, der Azure CLI und des Azure-Portals. Sie erstellen eine Ressourcengruppe zusammen mit einem Azure Data Lake Storage Gen2-Konto, das als Standardspeicher für den Arbeitsbereich als Teil des PowerShell-Skripts und des CLI-Skripts verwendet wird. Wenn Sie den Bereitstellungsprozess automatisieren möchten, rufen Sie diese PowerShell- oder CLI-Skripts über die DevOps-Releasepipeline auf.

### <a name="azure-portal"></a>Azure-Portal
Um einen Arbeitsbereich über das Azure-Portal zu erstellen, führen Sie die Schritte im [Schnellstart: Erstellen eines Synapse-Arbeitsbereichs](quickstart-create-workspace.md) aus.

### <a name="azure-powershell"></a>Azure PowerShell 
Mit dem folgenden Skript werden die Ressourcengruppe und der Azure Synapse-Arbeitsbereich erstellt, indem die Cmdlets „New-AzResourceGroup“ und „New-AzSynapseWorkspace“ verwendet werden.

#### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

```powershell
$storageAccountName= "<YourDefaultStorageAccountName>"
$resourceGroupName="<YourResourceGroupName>"
$regionName="<YourTargetRegionName>"
$containerName="<YourFileSystemName>" # This is the file system name
$workspaceName="<YourTargetRegionWorkspaceName>"

$sourcRegionWSName="<Your source region workspace name>"
$sourceRegionRGName="<YourSourceRegionResourceGroupName>"
$sqlUserName="<SQLUserName>"
$sqlPassword="<SQLStrongPassword>"

$sqlPoolName ="<YourTargetSQLPoolName>" #Both Source and target workspace SQL pool name will be same
$sparkPoolName ="<YourTargetWorkspaceSparkPoolName>"
$sparkVersion="2.4"

New-AzResourceGroup -Name $resourceGroupName -Location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Erstellen eines Data Lake Storage Gen2-Kontos

```powershell
#If the Storage account is already created, then you can skip this step.
New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location  $regionName `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $true 
```


#### <a name="create-an-azure-synapse-workspace"></a>Erstellen eines Azure Synapse-Arbeitsbereichs

```powershell
$password = ConvertTo-SecureString $sqlPassword -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ($sqlUserName, $password)

New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds 
```

Wenn Sie den Arbeitsbereich mit einem verwalteten Virtual Network erstellen möchten, fügen Sie dem Skript den zusätzlichen Parameter „ManagedVirtualNetwork“ hinzu. Weitere Informationen zu den verfügbaren Optionen finden Sie unter [Konfiguration verwalteter virtueller Netzwerke](/powershell/module/az.synapse/new-azsynapsemanagedvirtualnetworkconfig?view=azps-6.3.0&preserve-view=true).


```powershell
#Creating a managed virtual network configuration
$config = New-AzSynapseManagedVirtualNetworkConfig -PreventDataExfiltration -AllowedAadTenantIdsForLinking ContosoTenantId 

#Creating an Azure Synapse workspace
New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds `
                              -ManagedVirtualNetwork $config
```


### <a name="azure-cli"></a>Azure CLI

Dieses Azure CLI-Skript erstellt eine Ressourcengruppe, ein Data Lake Storage Gen2-Konto und ein Dateisystem. Anschließend erstellt es den Azure Synapse-Arbeitsbereich.

#### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

```azurecli
az group create --name $resourceGroupName --location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Erstellen eines Data Lake Storage Gen2-Kontos

```azurecli
# Checking if name is not used only then creates it.

$StorageAccountNameAvailable=(az storage account check-name --name $storageAccountName --subscription $subscriptionId | ConvertFrom-Json).nameAvailable

if($StorageAccountNameAvailable)
{
Write-Host "Storage account Name is available to be used...creating storage account"

#Creating an Data Lake Storage Gen2 account
$storgeAccountProvisionStatus=az storage account create `
  --name $storageAccountName `
  --resource-group $resourceGroupName `
  --location $regionName `
  --sku Standard_GRS `
  --kind StorageV2 `
  --enable-hierarchical-namespace $true

($storgeAccountProvisionStatus| ConvertFrom-Json).provisioningState
}
else
{
    Write-Host "Storage account Name is NOT available to be used...use another name --    exiting the script..."
    EXIT
}

#Creating a container in a Data Lake Storage Gen2 account

$key=(az storage account keys list -g $resourceGroupName -n $storageAccountName|ConvertFrom-Json)[0].value

$fileShareStatus=(az storage share create --account-name $storageAccountName --name $containerName --account-key $key)

if(($fileShareStatus|ConvertFrom-Json).created -eq "True")
{
      Write-Host f"Successfully created the fileshare - '$containerName'"
}
```


#### <a name="create-an-azure-synapse-workspace"></a>Erstellen eines Azure Synapse-Arbeitsbereichs

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $containerName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName
```
Um ein verwaltetes virtuelles Netzwerk zu aktivieren, schließen Sie den Parameter `--enable-managed-virtual-network` in das vorherige Skript ein. Weitere Optionen finden Sie unter [Arbeitsbereich: Verwaltetes virtuelles Netzwerk](/cli/azure/synapse/workspace?view=azure-cli-latest&preserve-view=true).

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $FileShareName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName `
  --enable-managed-virtual-network true `
  --allowed-tenant-ids "Contoso"
```

## <a name="step-2-create-an-azure-synapse-workspace-firewall-rule"></a>Schritt 2: Erstellen einer Firewallregel für den Azure Synapse-Arbeitsbereich 
Nachdem der Arbeitsbereich erstellt wurde, fügen Sie die Firewallregeln für den Arbeitsbereich hinzu. Beschränken Sie die IP-Adressen auf einen bestimmten Bereich. Sie können eine Firewallregel aus dem Azure-Portal oder mithilfe der PowerShell oder CLI hinzufügen.

### <a name="azure-portal"></a>Azure-Portal
Wählen Sie die Firewalloptionen aus, und fügen Sie den IP-Adressbereich hinzu, wie im folgenden Screenshot gezeigt.

:::image type="icon" source="media/how-to-move-workspace-from-one-region-to-another/firewall.png" border="false":::


### <a name="azure-powershell"></a>Azure PowerShell 
Führen Sie die folgenden PowerShell-Befehle aus, um Firewallregeln hinzuzufügen, indem Sie die Start- und End-IP-Adressen angeben. Aktualisieren Sie den IP-Adressbereich nach Ihren Anforderungen.


```powershell
$WorkspaceWeb = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Web
$WorkspaceDev = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Dev

# Adding firewall rules
$FirewallParams = @{
  WorkspaceName = $workspaceName
  Name = 'Allow Client IP'
  ResourceGroupName = $resourceGroup
  StartIpAddress = "0.0.0.0"
  EndIpAddress = "255.255.255.255"
}
New-AzSynapseFirewallRule @FirewallParams
```

Führen Sie das folgende Skript aus, um die SQL-Kontrolleinstellungen für eine verwaltete Identität im Arbeitsbereich zu aktualisieren:

```powershell 
Set-AzSynapseManagedIdentitySqlControlSetting -WorkspaceName $workspaceName -Enabled $true 
```

### <a name="azure-cli"></a>Azure CLI


```azurecli
az synapse workspace firewall-rule create --name allowAll --workspace-name $workspaceName  `
--resource-group $resourceGroupName --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
 
Führen Sie das folgende Skript aus, um die SQL-Kontrolleinstellungen für eine verwaltete Identität im Arbeitsbereich zu aktualisieren:

```azurecli
az synapse workspace managed-identity grant-sql-access `
--workspace-name $workspaceName --resource-group $resourceGroupName
```


## <a name="step-3-create-an-apache-spark-pool"></a>Schritt 3: Erstellen eines Apache Spark-Pools

Erstellen Sie den Spark-Pool mit derselben Konfiguration, die im Arbeitsbereich der Quellregion verwendet wird.

### <a name="azure-portal"></a>Azure-Portal

Informationen zum Erstellen eines Spark-Pools über das Azure-Portal finden Sie im [Schnellstart: Erstellen eines neuen serverlosen Apache Spark-Pools mithilfe des Azure-Portals](quickstart-create-apache-spark-pool-portal.md).


Sie können den Spark-Pool auch über Synapse Studio erstellen, indem Sie die Schritte im [Schnellstart: Erstellen eines serverlosen Apache Spark-Pools mithilfe von Synapse Studio](quickstart-create-apache-spark-pool-studio.md) befolgen.

### <a name="azure-powershell"></a>Azure PowerShell

Das folgende Skript erstellt einen Spark-Pool mit zwei Workern und einem Treiberknoten. Aktualisieren Sie die Werte so, dass sie mit ihrem Spark-Pool im Arbeitsbereich der Quellregion übereinstimmen.


```powershell
#Creating a Spark pool with 3 nodes (2 worker + 1 driver) and a small cluster size with 4 cores and 32 GB RAM. 
New-AzSynapseSparkPool `
    -WorkspaceName  $workspaceName `
    -Name $sparkPoolName `
    -NodeCount 3 `
    -SparkVersion $sparkVersion `
    -NodeSize Small
```
 
### <a name="azure-cli"></a>Azure CLI

```azurecli
az synapse spark pool create --name $sparkPoolName --workspace-name $workspaceName --resource-group $resourceGroupName `
--spark-version $sparkVersion --node-count 3 --node-size small
```
## <a name="move"></a>Move
## <a name="step-4-restore-a-dedicated-sql-pool"></a>Schritt 4: Wiederherstellen eines dedizierten SQL-Pools 

### <a name="restore-from-geo-redundant-backups"></a>Wiederherstellen aus georedundanten Sicherungen

Informationen zum Wiederherstellen der dedizierten SQL-Pools aus einer Geosicherung mithilfe des Azure-Portals und der PowerShell finden Sie unter [Geowiederherstellung eines dedizierten SQL-Pools in Azure Synapse Analytics](sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup.md).


### <a name="restore-by-using-restore-points-from-the-source-region-workspace-dedicated-sql-pool"></a>Wiederherstellen mithilfe von Wiederherstellungspunkten aus dem dedizierten SQL-Pool im Arbeitsbereich der Quellregion

Stellen Sie den dedizierten SQL-Pool mithilfe des Wiederherstellungspunkts des dedizierten SQL-Pools im Arbeitsbereich der Quellregion im Arbeitsbereich der Zielregion wieder her. Sie können das Azure-Portal, Synapse Studio oder PowerShell verwenden, um aus Wiederherstellungspunkten wiederherzustellen. Wenn auf die Quellregion nicht zugegriffen werden kann, können Sie nicht mit dieser Option wiederherstellen.

#### <a name="synapse-studio"></a>Synapse Studio

In Synapse Studio können Sie den dedizierten SQL-Pool mithilfe von *Wiederherstellungspunkten* aus jedem Arbeitsbereich im Abonnement wiederherstellen. Wählen Sie beim Erstellen des dedizierten SQL-Pools unter **Zusätzliche Einstellungen** die Option **Wiederherstellungspunkt** aus, und wählen Sie den Arbeitsbereich aus, wie im folgenden Screenshot gezeigt. Wenn Sie einen benutzerdefinierten Wiederherstellungspunkt erstellt haben, verwenden Sie ihn, um den SQL-Pool wiederherzustellen. Andernfalls können Sie den letzten automatischen Wiederherstellungspunkt auswählen.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/restore-sql-pool.png" alt-text="Wiederherstellen des SQL Pools":::


#### <a name="azure-powershell"></a>Azure PowerShell

Führen Sie das folgende PowerShell-Skript aus, um den Arbeitsbereich wiederherzustellen. Dieses Skript verwendet den letzten Wiederherstellungspunkt aus dem dedizierten SQL-Pool des Quellarbeitsbereichs, um den SQL im Zielarbeitsbereich wiederherzustellen. Aktualisieren Sie vor dem Ausführen des Skripts die Leistungsstufe von DW100c auf den erforderlichen Wert. 

> [!IMPORTANT]
> Der Name des dedizierten SQL-Pools sollte in beiden Arbeitsbereichen identisch sein.


```powershell
#Getting the restore points
$restorePoint=Get-AzSynapseSqlPoolRestorePoint -WorkspaceName $sourceRegionWSName -Name $sqlPoolName|Sort-Object  -Property RestorePointCreationDate -Descending `
                                                                                         | SELECT RestorePointCreationDate -ExpandProperty  RestorePointCreationDate -First 1
```
 
 

```powershell
<#
Transform Synapse SQL pool resource ID to SQL database ID because currently the command only accepts the SQL database ID. 
For example: /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Sql/servers/<WorkspaceName>/databases/<DatabaseName>
#>
$pool = Get-AzSynapseSqlPool -ResourceGroupName $sourceRegionRGName -WorkspaceName $sourcRegionWSName -Name $sqlPoolName
$databaseId = $pool.Id `
    -replace "Microsoft.Synapse", "Microsoft.Sql" `
    -replace "workspaces", "servers" `
    -replace "sqlPools", "databases" 
 

$restoredPool = Restore-AzSynapseSqlPool -FromRestorePoint `
                                         -RestorePoint $restorePoint `
                                         -TargetSqlPoolName $sqlPoolName `
                                         -ResourceGroupName $resourceGroupName `
                                         -WorkspaceName $workspaceName `
                                         -ResourceId $databaseId `
                                         -PerformanceLevel DW100c -AsJob


#Tracks the status of the restore 

Get-Job | Where-Object Command -In ("Restore-AzSynapseSqlPool") | `
Select-Object Id,Command,JobStateInfo,PSBeginTime,PSEndTime,PSJobTypeName,Error |Format-Table
```
Nachdem der dedizierte SQL-Pool wiederhergestellt wurde, erstellen Sie alle SQL-Anmeldungen in Azure Synapse. Um alle Anmeldungen zu erstellen, führen Sie die Schritte unter [Anmeldung erstellen](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true) aus.

## <a name="step-5-create-a-serverless-sql-pool-spark-pool-database-and-objects"></a>Schritt 5: Erstellen eines serverlosen SQL-Pools, einer Spark-Pool-Datenbank und von Objekten

Sie können serverlose SQL-Pooldatenbanken und Spark-Pools nicht sichern und wiederherstellen. Als mögliche Problemumgehung haben Sie eventuell folgende Möglichkeiten:

1. Erstellen von Notebooks und SQL-Skripts, die über den Code verfügen, um alle erforderlichen Spark-Pools, serverlosen SQL-Pooldatenbanken, Tabellen, Rollen und Benutzer mit allen Rollenzuweisungen neu zu erstellen. Einchecken dieser Artefakte in Azure DevOps oder GitHub.
1. Wenn der Name des Speicherkontos geändert wird, stellen Sie sicher, dass die Codeartefakte auf den richtigen Speicherkontonamen verweisen.
1. Erstellen von Pipelines, die diese Codeartefakte in einer bestimmten Abfolge aufrufen. Wenn diese Pipelines im Arbeitsbereich der Zielregion ausgeführt werden, werden die Spark SQL-Datenbanken, serverlosen SQL-Pooldatenbanken, externen Datenquellen, Ansichten, Rollen und Benutzer sowie Berechtigungen im Arbeitsbereich der Zielregion erstellt.
1. Wenn Sie den Arbeitsbereich der Quellregion in Azure DevOps integrieren, werden diese Codeartefakte Teil des Repositorys. Später können Sie diese Codeartefakte im Arbeitsbereich der Zielregion bereitstellen, indem Sie die DevOps-Releasepipeline verwenden, wie in Schritt 6 erwähnt.
1. Lösen Sie diese Pipelines manuell im Arbeitsbereich der Zielregion aus.

## <a name="step-6-deploy-artifacts-and-pipelines-by-using-cicd"></a>Schritt 6: Bereitstellen von Artefakten und Pipelines mithilfe von CI/CD 

 Um zu erfahren, wie Sie einen Azure Synapse-Arbeitsbereich in Azure DevOps oder GitHub integrieren und die Artefakte in einem Arbeitsbereich der Zielregion bereitstellen, führen Sie die Schritte unter [Continuous Integration und Continuous Delivery (CI/CD) für einen Azure Synapse-Arbeitsbereich](cicd/continuous-integration-delivery.md) aus. 

Nachdem der Arbeitsbereich in Azure DevOps integriert wurde, gibt es einen Branch mit dem Namen „workspace_publish“. Dieser Branch enthält die Arbeitsbereichsvorlage, die Definitionen für die Artefakte wie Notebooks, SQL-Skripts, Datasets, verknüpfte Dienste, Pipelines, Trigger und Spark-Auftragsdefinitionen enthält.

Dieser Screenshot aus dem Azure DevOps-Repository zeigt die Vorlagendateien des Arbeitsbereichs für die Artefakte und anderen Komponenten.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/devops-repo-workspace-publish.png" alt-text="Screenshot von „workspace_publish“.":::

Sie können die Arbeitsbereichsvorlage verwenden, um Artefakte und Pipelines in einem Arbeitsbereich bereitzustellen, indem Sie die Azure DevOps-Releasepipeline verwenden, wie im folgenden Screenshot gezeigt.

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/release-pipeline.png" alt-text="Screenshot der Synapse-Releasepipeline.":::

Wenn der Arbeitsbereich nicht in GitHub oder Azure DevOps integriert ist, müssen Sie benutzerdefinierte PowerShell- oder Azure CLI-Skripts manuell neu erstellen oder schreiben, um alle Artefakte, Pipelines, verknüpften Dienste, Anmeldeinformationen, Trigger und Spark-Definitionen im Arbeitsbereich der Zielregion bereitzustellen.


> [!NOTE]
> Bei diesem Prozess müssen Sie die Pipelines und Codeartefakte ständig aktualisieren, damit alle Änderungen, die an Spark- und serverlosen SQL-Pools, Objekten und Rollen in den Arbeitsbereichen der Quellregion vorgenommen werden, enthalten sind.

## <a name="step-7-create-a-shared-integration-runtime"></a>Schritt 7: Erstellen einer freigegebenen Integration Runtime

Um eine SHIR zu erstellen, führen Sie die Schritte unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md) aus.

## <a name="step-8-assign-an-azure-role-to-managed-identity"></a>Schritt 8: Zuweisen einer Azure-Rolle zu einer verwalteten Identität

 Weisen Sie der verwalteten Identität des neuen Arbeitsbereichs `Storage Blob Contributor`-Zugriff auf das standardmäßig angefügten Data Lake Storage Gen2-Konto zu. Weisen Sie außerdem Zugriff auf andere Speicherkonten zu, bei denen SA-MI für die Authentifizierung verwendet wird. Weisen Sie Azure AD-Benutzern und -Gruppen für alle erforderlichen Speicherkonten `Storage Blob Contributor`- oder `Storage Blob Reader`-Zugriff zu.

### <a name="azure-portal"></a>Azure-Portal
Führen Sie die Schritte unter [Erteilen von Berechtigungen für die verwaltete Identität des Arbeitsbereichs](security/how-to-grant-workspace-managed-identity-permissions.md) aus, um der verwalteten Identität des Arbeitsbereichs die Rolle „Mitwirkender an Storage-Blobdaten“ zuzuweisen.

### <a name="azure-powershell"></a>Azure PowerShell
Weisen Sie der verwalteten Identität des Arbeitsbereichs die Rolle „Mitwirkender an Storage-Blobdaten“ zu.

```powershell

$workSpaceIdentityObjectID= (Get-AzSynapseWorkspace -ResourceGroupName $resourceGroupName -Name $workspaceName).Identity.PrincipalId 
$scope = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Storage/storageAccounts/$($storageAccountName)"

# Adding Storage Blob Data Contributor to WS Managed Identity on the storage account. This errors out with the message New-AzRoleAssignment : Exception of type 'Microsoft.Rest.Azure.CloudException' was thrown.
# But it creates the required permissions on the storage account.
$roleAssignedforManagedIdentity=New-AzRoleAssignment -ObjectId $workSpaceIdentityObjectID `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope $scope -ErrorAction SilentlyContinue
```


### <a name="azure-cli"></a>Azure CLI

```azurecli
# Getting Role name
$roleName =az role definition list --query "[?contains(roleName, 'Storage Blob Data Contributor')].{roleName:roleName}" --output tsv

#Getting resource id for storage account
$scope= (az storage account show --name $storageAccountName|ConvertFrom-Json).id

#Getting principal ID for WS Managed Identity
$workSpaceIdentityObjectID=(az synapse workspace show --name $workspaceName --resource-group $resourceGroupName|ConvertFrom-Json).Identity.PrincipalId 
                    
# Adding Storage Blob Data Contributor Azure role to SA-MI
az role assignment create --assignee $workSpaceIdentityObjectID `
--role $roleName `
--scope $scope
```

## <a name="step-9-assign-azure-synapse-rbac-roles"></a>Schritt 9: Zuweisen von RBAC-Rollen von Azure Synapse

Fügen Sie alle Benutzer, die Zugriff auf den Zielarbeitsbereich benötigen, mit separaten Rollen und Berechtigungen hinzu. Mit dem folgenden PowerShell- und CLI-Skript wird der Rolle „Synapse-Administrator“ im Arbeitsbereich der Zielregion ein Azure AD-Benutzer hinzugefügt. Informationen zum Abrufen aller RBAC-Rollennamen von Azure Synapse finden Sie unter [RBAC-Rollen von Azure Synapse](security/synapse-workspace-synapse-rbac-roles.md).

### <a name="synapse-studio"></a>Synapse Studio

Um RBAC-Zuweisungen von Azure Synapse aus Synapse Studio hinzuzufügen oder zu löschen, führen Sie die Schritte unter [Verwalten von RBAC-Rollenzuweisungen von Azure Synapse in Synapse Studio](security/how-to-manage-synapse-rbac-role-assignments.md) aus.


### <a name="azure-powershell"></a>Azure PowerShell

Das folgende PowerShell-Skript fügt die „Synapse-Administrator“-Rollenzuweisung einem Azure AD-Benutzer oder einer Azure AD-Gruppe hinzu. Sie können „-RoleDefinitionId“ anstelle von „-RoleDefinitionName“ mit dem folgenden Befehl verwenden, um die Benutzer dem Arbeitsbereich hinzuzufügen:

```powershell
# Add the Synapse RBAC assignment. Use the objectId of the Azure AD user or group you want to assign.
New-AzSynapseRoleAssignment `
   -WorkspaceName $workspaceName  `
   -RoleDefinitionName "Synapse Administrator" `
   -ObjectId 1c02d2a6-ed3d-46ec-b578-6f36da5819c6

# Check if user is added to the access control by running this command.
Get-AzSynapseRoleAssignment -WorkspaceName $workspaceName  
```

Um die ObjectIds und RoleIds im Arbeitsbereich der Quellregion abzurufen, führen Sie den Befehl „Get-AzSynapseRoleAssignment“ aus. Weisen Sie dieselben RBAC-Rollen von Azure Synapse Azure AD-Benutzern oder -Gruppen im Arbeitsbereich der Zielregion zu.

Anstatt „-ObjectId“ als Parameter zu verwenden, können Sie auch „-SignInName“ verwenden, mit dem Sie die E-Mail-Adresse oder den Benutzerprinzipalnamen des Benutzers angeben. Weitere Informationen zu den verfügbaren Optionen finden Sie unter [Azure Synapse RBAC – PowerShell-Cmdlet](/powershell/module/az.synapse/new-azsynapseroleassignment?view=azps-6.3.0&preserve-view=true). 

### <a name="azure-cli"></a>Azure CLI

```azurecli
#Get the Object Id of the user and assign the required Azure Synapse RBAC permissions to the Azure AD user. You can provide the email address of the user (username@contoso.com) for the --assignee parameter.
az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Administrator" --assignee adasdasdd42-0000-000-xxx-xxxxxxx

az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Contributor" --assignee "user1@contoso.com"

```

Weitere Informationen zu den verfügbaren Optionen finden Sie unter [Azure Synapse RBAC – CLI](/cli/azure/synapse/role/assignment?view=azure-cli-latest&preserve-view=true). 

## <a name="step-10-upload-workspace-packages"></a>Schritt 10: Hochladen von Arbeitsbereichspaketen

Laden Sie alle erforderlichen Arbeitsbereichspakete in den neuen Arbeitsbereich hoch. Informationen zum Automatisieren des Uploadvorgangs für die Arbeitsbereichspakete finden Sie in der [Microsoft Azure Synapse Analytics-Artefakte-Clientbibliothek](https://www.nuget.org/packages/Azure.Analytics.Synapse.Artifacts/1.0.0-preview.10).

## <a name="step-11-permissions"></a>Schritt 11: Berechtigungen
    
Führen Sie zum Einrichten der Zugriffssteuerung für den Azure Synapse-Arbeitsbereich der Zielregion die Schritte unter [Einrichten der Zugriffssteuerung für Ihren Azure Synapse-Arbeitsbereich](security/how-to-set-up-access-control.md) aus. 


## <a name="step-12-create-managed-private-endpoints"></a>Schritt 12: Erstellen verwalteter privater Endpunkte

Informationen zum erneuten Erstellen der verwalteten privaten Endpunkte aus dem Arbeitsbereich der Quellregion in Ihrem Arbeitsbereich der Zielregion finden Sie unter [Erstellen eines verwalteten privaten Endpunkts für Ihre Datenquelle](security/how-to-create-managed-private-endpoints.md). 

## <a name="discard"></a>Verwerfen
Falls Sie den Arbeitsbereich der Zielregion verwerfen möchten, löschen Sie den Arbeitsbereich der Zielregion. Gehen Sie dazu von Ihrem Dashboard im Portal zur Ressourcengruppe, wählen Sie den Arbeitsbereich aus und wählen Sie oben auf der Seite der Ressourcengruppe Löschen.

## <a name="clean-up"></a>Bereinigen
Um die Änderungen zu bestätigen und die Verschiebung des Arbeitsbereichs abzuschließen, löschen Sie den Arbeitsbereich der Quellregion, nachdem Sie den Arbeitsbereich in der Zielregion getestet haben. Gehen Sie dazu von Ihrem Dashboard im Portal zu der Ressourcengruppe, die den Arbeitsbereich der Quellregion enthält, wählen Sie den Arbeitsbereich aus und wählen Sie oben auf der Seite der Ressourcengruppe Löschen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Verwaltete virtuelle Netzwerke in Azure Synapse](security/synapse-workspace-managed-vnet.md).
- Erfahren Sie mehr über [Verwaltete private Endpunkte in Azure Synapse](security/synapse-workspace-managed-private-endpoints.md).
- Erfahren Sie mehr über das [Herstellen einer Verbindung mit Arbeitsbereichsressourcen über ein eingeschränktes Netzwerk](security/how-to-connect-to-workspace-from-restricted-network.md).
