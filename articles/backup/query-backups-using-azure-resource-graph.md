---
title: Abfragen Ihrer Sicherungen mithilfe von Azure Resource Graph (ARG)
description: Erfahren Sie mehr über das Abfragen von Informationen zu Sicherungen für Ihre Azure-Ressourcen mithilfe von Azure Resource Graph (ARG).
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: e9caa1d4d8de77efe9acb31c0cec3be5741b69c7
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236046"
---
# <a name="query-your-backups-using-azure-resource-graph-arg"></a>Abfragen Ihrer Sicherungen mithilfe von Azure Resource Graph (ARG)

Sie können Informationen zu Sicherungen für Ihre Azure-Ressourcen ohne zusätzliche Kosten mithilfe von [Azure Resource Graph (ARG)](../governance/resource-graph/overview.md) abfragen. ARG ist ein Azure-Dienst, der die Azure-Ressourcenverwaltung erweitert. Es bietet effiziente Möglichkeiten der Ressourcenuntersuchung und ermöglicht es, einen bestimmten Satz von Abonnements bedarfsgerecht abzufragen. Im Folgenden sind die Hauptvorteile der Verwendung von ARG zum Abfragen Ihrer Sicherungsmetadaten aufgeführt:

- Möglichkeiten zum Abfragen von umfangreicher Ressourcen mit komplexem Filtern, Gruppieren und Sortieren nach Eigenschaften der Ressource.
- Möglichkeit zum Abrufen von Echtzeitinformationen zu Ihren Sicherungen, einschließlich von in Bearbeitung befindlichen Sicherungsaufträgen.
- Möglichkeit, sicherungsbezogene Daten mit nützlichen Informationen zu verwandten Azure-Ressourcen wie Azure Virtual Machines und Speicherkonten zu verbinden.

## <a name="getting-started"></a>Erste Schritte

Führen Sie die folgenden Schritte aus, um mit dem Abfragen Ihrer Sicherungen mit ARG zu beginnen:

1. Suchen Sie im Azure-Portal nach _Resource Graph-Explorer_. Wählen Sie diese Option aus, um zum ARG-Abfrage-Editor umgeleitet zu werden.
    
    ![Azure-Ressourcengruppe durchsuchen](./media/query-backups-using-azure-resource-graph/search-resource-graph-explorer.png)

    Im linken Bereich werden alle Tabellen (und die zugehörigen Schemas) angezeigt, die für Abfragen verfügbar sind.
    
    - Die Tabelle **RecoveryServicesResources** enthält die meisten sicherungsbezogenen Datensätze, z. B. Auftragsdetails und Sicherungsinstanzdetails.  usw.
    - Die Tabelle **Ressourcen** enthält Informationen zu allen Azure-Ressourcen der obersten Ebene, z. B. Recovery Services-Tresore, Azure Virtual Machines, Speicherkonten und so weiter.

    ![Verfügbare Tabellen und zugeordnete Schemas für Abfragen](./media/query-backups-using-azure-resource-graph/tables-and-associated-schemas.png)

1. Um die Daten in einer dieser Tabellen zu untersuchen, schreiben Sie **Kusto**-Abfragen im Abfrage-Editor, und klicken Sie auf **Abfrage ausführen**.

    Sie können die Ausgabe dieser Abfragen aus dem **Resource Graph-Explorer** heraus als CSV herunterladen. Sie können diese Abfragen auch in einer benutzerdefinierten Automatisierung mit jedem der Automatisierungsclients verwenden, die von ARG unterstützt werden, z. B. [PowerShell](../governance/resource-graph/first-query-powershell.md), [CLI](../governance/resource-graph/first-query-azurecli.md) oder [SDK](../governance/resource-graph/first-query-python.md). Sie können auch [benutzerdefinierte Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md) im Azure-Portal erstellen, indem Sie ARG als Datenquelle verwenden.

>[!NOTE] 
>- Sicherungs-/Wiederherstellungsaufträge, die bis zu 14 Tage alt sind, stehen in ARG für Abfragen zur Verfügung. Wenn Sie historische Datensätze abfragen möchten, empfiehlt es sich, **Azure Monitor-Protokolle** zu verwenden.
>- Mit ARG können Sie die Ressourcen abfragen, für die Sie über die entsprechenden RBAC-Rechte verfügen.

## <a name="sample-queries"></a>Beispielabfragen

Im Folgenden finden Sie einige ARG-Beispielabfragen für Ihre Sicherungsdaten, die Sie in benutzerdefinierten Dashboards und Automatisierungen verwenden können.

### <a name="list-all-azure-vms-that-have-been-configured-for-backup"></a>Auflisten aller Azure-VMs, die für die Sicherung konfiguriert wurden

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupInstances',split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend dataSourceType = case(type=~'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupInstances',properties.dataSourceSetInfo.datasourceType,'--')
| extend friendlyName = properties.friendlyName
| extend dsResourceGroup = split(split(properties.dataSourceInfo.resourceID, '/resourceGroups/')[1],'/')[0]
| extend dsSubscription = split(split(properties.dataSourceInfo.resourceID, '/subscriptions/')[1],'/')[0]
| extend lastRestorePoint = properties.lastRecoveryPoint
| extend primaryLocation = properties.dataSourceInfo.resourceLocation
| extend policyName = case(type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.policyName, type =~ 'microsoft.dataprotection/backupVaults/backupInstances', properties.policyInfo.name, '--')
| extend protectionState = properties.currentProtectionState
| where protectionState in~ ('ConfiguringProtection','ProtectionConfigured','ConfiguringProtectionFailed','ProtectionStopped','SoftDeleted','ProtectionError')
| where (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('AzureIaasVM')) //add the relevant subscription ids you wish to query to this line

```

### <a name="list-all-backup-jobs-on-azure-databases-for-postgresql-servers-in-the-last-one-week"></a>Auflisten aller Sicherungsaufträge auf Azure-Datenbanken für PostgreSQL-Server in der letzten Woche

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/backupVaults/backupJobs')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
| extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
| extend backupInstanceName = properties.backupInstanceId
| extend dsResourceGroup = split(split(properties.dataSourceId, '/resourceGroups/')[1],'/')[0]| extend dsSubscription = split(split(properties.dataSourceId, '/subscriptions/')[1],'/')[0]
| extend status = properties.status
| extend dataSourceId = properties.dataSourceId
| extend primaryLocation = properties.dataSourceLocation
| extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'),startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration
| project id, name, friendlyName, resourceGroup, vaultName, dataSourceType, operation, jobStatus, startTime, duration, backupInstanceName, dsResourceGroup, dsSubscription, status, primaryLocation, dataSourceId
| where  (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases')) and (status in~ ('Started','InProgress','Succeeded','Completed','Failed','CompletedWithWarnings')) and (operation in~ ('adhocBackup','backup')) //add the relevant subscription ids you wish to query to this line
| where (startTime >= ago(7d))

```

### <a name="list-all-azure-vms-that-have-not-been-configured-for-backup"></a>Auflisten aller Azure-VMs, die nicht für die Sicherung konfiguriert wurden

```kusto
Resources
| where type in~ ('microsoft.compute/virtualmachines','microsoft.classiccompute/virtualmachines') 
| extend resourceId=tolower(id) 
| join kind = leftouter ( RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| where properties.backupManagementType == "AzureIaasVM"
| project resourceId = tolower(tostring(properties.sourceResourceId)), backupItemid = id, isBackedUp = isnotempty(id) ) on resourceId 
| extend isProtected = isnotempty(backupItemid)
| where (isProtected == (0))
| project id,name,resourceGroup,location,tags

```

### <a name="list-all-backup-policies-used-for-azure-vms"></a>Auflisten aller für Azure-VMs verwendeten Sicherungsrichtlinien

```kusto
RecoveryServicesResources
| where type == 'microsoft.recoveryservices/vaults/backuppolicies'
| extend vaultName = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', properties.backupManagementType,type == 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where datasourceType == 'AzureIaasVM'
```

### <a name="list-all-vms-associated-with-a-given-backup-policy"></a>Auflisten aller VMs, die einer bestimmten Sicherungsrichtlinie zugeordnet sind

```kusto
RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| project propertiesJSON = parse_json(properties)
| where propertiesJSON.backupManagementType == "AzureIaasVM"
| project VMID=propertiesJSON.sourceResourceId, PolicyID=propertiesJSON.policyId
| where PolicyID == "<ARM ID of the given policy>"
```

### <a name="list-all-backup-policies-used-for-azure-databases-for-postgresql-servers"></a>Auflisten aller Sicherungsrichtlinien, die für Azure-Datenbanken für PostgreSQL-Server verwendet werden

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/BackupVaults/backupPolicies')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupPolicies', split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'microsoft.recoveryservices/vaults/backupPolicies', split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupPolicies', properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where (datasourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases'))

```

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zu Azure Resource Graph](../governance/resource-graph/overview.md)
