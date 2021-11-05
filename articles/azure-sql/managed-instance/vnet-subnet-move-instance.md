---
title: Verschieben einer verwalteten Instanz in ein anderes Subnetz
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie, wie Sie eine Azure SQL Managed Instance mit nur kurzer Ausfallzeit (in der Regel bis zu 10 Sekunden) während des Failovers in ein anderes Subnetz verschieben.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7adb9886bdeebc8aad7f8c200b21523e53651a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095168"
---
# <a name="move-azure-sql-managed-instance-across-subnets"></a>Verschieben von Azure SQL Managed Instance zwischen Subnetzen
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance muss in einem dedizierten Subnetz in einem [virtuellen Azure-Netzwerk](../../virtual-network/virtual-networks-overview.md) bereitgestellt werden. Die Anzahl der verwalteten Instanzen, die im Subnetz bereitgestellt werden können, hängt von der Größe des Subnetzes (dem Subnetzbereich) ab.

In diesem Artikel erfahren Sie, wie Sie Ihre verwaltete Instanz aus einem Subnetz in ein anderes verschieben, ähnlich wie beim Skalieren virtueller Kerne oder Ändern der Dienstebene der Instanz. SQL Managed Instance ist während der Verschiebung verfügbar, mit Ausnahme einer kurzen Ausfallzeit, die durch ein Failover am Ende des Updates verursacht wird. Diese Ausfallzeit dauert in der Regel bis zu 10 Sekunden, auch wenn Transaktionen mit langer Ausführungszeit unterbrochen werden. 

Das Verschieben der Instanz in ein anderes Subnetz löst die folgenden Vorgänge für virtuelle Cluster aus:
- Das Zielsubnetz erstellt den virtuellen Cluster oder ändert seine Größe.
- Der virtuelle Cluster wird im Quellsubnetz entfernt oder defragmentiert. 

Bevor Sie Ihre Instanz in ein anderes Subnetz verschieben, sollten Sie sich mit den folgenden Konzepten vertraut machen: 
- [Bestimmen von Subnetzgröße und -bereich für Azure SQL Managed Instance](vnet-subnet-determine-size.md).
- Wählen zwischen dem Verschieben der Instanz in ein [neues Subnetz](virtual-network-subnet-create-arm-template.md) oder der [Verwendung eines vorhandenen Subnetzes](vnet-existing-add-subnet.md).
- Verwenden von [Verwaltungsvorgängen](management-operations-overview.md), um neue verwaltete Instanzen automatisch bereitzustellen, Instanzeigenschaften zu aktualisieren oder Instanzen zu löschen. Es ist möglich, diese Verwaltungsvorgänge zu [überwachen](management-operations-monitor.md). 



## <a name="requirements-and-limitations"></a>Anforderungen und Einschränkungen 

Um eine verwaltete Instanz bereitzustellen oder in ein anderes Subnetz zu verschieben, muss das Zielsubnetz bestimmte [Netzwerkanforderungen](connectivity-architecture-overview.md#service-aided-subnet-configuration) erfüllen.

### <a name="subnet-readiness"></a>Subnetzbereitschaft 

Vergewissern Sie sich vor dem Verschieben Ihrer verwalteten Instanz, dass das Subnetz als **Bereit für verwaltete Instanz** markiert ist. 

In der Benutzeroberfläche des **virtuellen Netzwerks** im Azure-Portal werden virtuelle Netzwerke, die die Voraussetzungen für eine verwaltete Instanz erfüllen, als **Bereit für verwaltete Instanz** kategorisiert. Virtuelle Netzwerke, in denen Subnetze mit bereits bereitgestellten verwalteten Instanzen vorhanden sind, zeigen ein Symbol vor dem Namen des virtuellen Netzwerks an. Leere Subnetze, die für eine verwaltete Instanz bereit sind, verfügen nicht über ein Symbol. 

Subnetze, die als **Andere** gekennzeichnet sind, sind leer und können für eine verwaltete Instanz verwendet werden. Zunächst müssen Sie jedoch die [Netzwerkanforderungen](connectivity-architecture-overview.md#service-aided-subnet-configuration) erfüllen. Dies schließt Folgendes ein:

- Delegieren an den Ressourcenanbieter Microsoft.Sql/managedInstances
- Anfügen einer Routingtabelle
- Anfügen einer Netzwerksicherheitsgruppe

Nachdem alle Anforderungen erfüllt wurden, wechselt das Subnetz aus der Kategorie **Andere** in die Kategorie **Bereit für verwaltete Instanz** und kann für eine verwaltete Instanz verwendet werden. 

Subnetze, die als **Ungültig** markiert sind, können nicht für neue oder vorhandene verwaltete Instanzen verwendet werden, weil sie bereits in Gebrauch sind (Instanzen, die für Instanzbereitstellungen verwendet werden, dürfen keine anderen Ressourcen enthalten), oder das Subnetz weist eine andere DNS-Zone auf (eine subnetzübergreifende Instanzverschiebeeinschränkung). 

> [!div class="mx-imgBorder"]
> ![Screenshot: Dropdownliste „Azure SQL Managed Instance-Subnetz“](./media/vnet-subnet-move-instance/subnet-grouping-per-state.png)


Abhängig vom Subnetzstatus und -ziel können die folgenden Anpassungen am Zielsubnetz vorgenommen werden: 

- **Bereit für verwaltete Instanz (enthält vorhandene SQL Managed Instance)** : Es werden keine Anpassungen vorgenommen. Diese Subnetze enthalten bereits verwaltete Instanzen, und jede Änderung am Subnetz kann sich auf vorhandene Instanzen auswirken. 
- **Bereit für verwaltete Instanz (leer)** : Der Workflow überprüft alle erforderlichen Regeln in der Netzwerksicherheitsgruppe und der Routingtabelle und fügt alle erforderlichen, aber fehlenden Regeln hinzu. <sup>1</sup>

> [!Note]
> <sup>1</sup> Der Konfiguration des Quellsubnetzes hinzugefügte benutzerdefinierte Regeln werden nicht in das Zielsubnetz kopiert. Jede Anpassung der Konfiguration des Quellsubnetzes muss manuell in das Zielsubnetz repliziert werden. Eine Möglichkeit, dies zu erreichen, ist die Verwendung derselben Routingtabelle und Netzwerksicherheitsgruppe für das Quell- und Zielsubnetz.


### <a name="destination-subnet-limitations"></a>Einschränkungen des Zielsubnetzes 

Beachten Sie bei der Auswahl eines Zielsubnetzes für eine vorhandene Instanz die folgenden Einschränkungen: 

- Das Zielsubnetz muss sich im gleichen virtuellen Netzwerk wie das Quellsubnetz befinden. 
- Die DNS-Zone des Zielsubnetzes muss mit der DNS-Zone des Quellsubnetzes übereinstimmen, da das Ändern der DNS-Zone einer verwalteten Instanz derzeit nicht unterstützt wird. 
- Instanzen, die auf Gen4-Hardware ausgeführt werden, müssen auf eine neuere Hardwaregeneration aktualisiert werden, da Gen4 als veraltet gilt. Ein Upgrade der Hardwaregeneration und das Verschieben in ein anderes Subnetz kann in einem Vorgang durchgeführt werden. 


## <a name="operation-steps"></a>Auszuführende Schritte

In der folgenden Tabelle werden die Vorgangsschritte aufgeführt, die während des Instanzverschiebevorgangs ausgeführt werden: 

|Schrittname  |Beschreibung des Schritts  |
|----|---------|
|Anforderungsvalidierung |Überprüft die übermittelten Parameter. Wenn eine Fehlkonfiguration erkannt wird, schlägt der Vorgang mit einer Fehlermeldung fehl. |
|Änderung der Größe/Erstellung eines virtuellen Clusters |Je nach Status des Zielsubnetzes wird der virtuelle Cluster entweder erstellt oder seine Größe geändert.  |
|Start der neuen Instanz |Der SQL-Prozess wird im bereitgestellten virtuellen Cluster im Zielsubnetz gestartet. |
|Seeding/Anfügen von Datenbankdateien |Je nach Dienstebene wird entweder Seeding für die Datenbank ausgeführt, oder die Datenbankdateien werden angefügt. |
|Failovervorbereitung und -durchführung |Nachdem das Seeding der Daten ausgeführt oder die Datenbankdateien erneut angefügt wurden, bereitet das System das Failover vor. Wenn alles bereit ist, führt das System ein Failover **mit einer kurzen Ausfallzeit** aus, die in der Regel weniger als 10 Sekunden beträgt.  |
|Bereinigung der alte SQL-Instanz |Entfernt den alten SQL-Prozess aus dem virtuellen Quellcluster.  |
|Löschung eines virtuellen Clusters |Wenn es sich um die letzte Instanz innerhalb des Quellsubnetzes handelt, wird der virtuelle Cluster im letzten Schritt synchron gelöscht. Andernfalls wird der virtuelle Cluster asynchron defragmentiert.  |

Eine ausführliche Erläuterung der Vorgangsschritte finden Sie in der [Übersicht über Azure SQL Managed Instance-Verwaltungsvorgänge](management-operations-overview.md#management-operations-steps).

## <a name="move-the-instance"></a>Verschieben der Instanz

Eine subnetzübergreifende Instanzverschiebung ist Teil des Instanzaktualisierungsvorgangs. Die vorhandene Instanzaktualisierungs-API, Azure PowerShell und Azure CLI-Befehle wurden um eine Subnetz-ID-Eigenschaft erweitert. 

Verwenden Sie im Azure-Portal das Subnetzfeld auf dem Blatt **Netzwerk**, um die Instanz in das Zielsubnetz zu verschieben. Wenn Sie Azure PowerShell oder die Azure CLI verwenden, geben Sie im Aktualisierungsbefehl eine andere Subnetz-ID an, um die Instanz aus einem vorhandenen Subnetz in das Zielsubnetz zu verschieben. 

Eine vollständige Referenz der Instanzverwaltungsbefehle finden Sie unter [Verwaltungs-API-Referenz für Azure SQL Managed Instance](api-references-create-manage-instance.md). 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Die Option zum Auswählen des Instanzsubnetzes befindet sich auf dem Blatt **Netzwerk** des Azure-Portals. Der Instanzverschiebevorgang wird gestartet, wenn Sie ein Subnetz auswählen und Ihre Änderungen speichern. 

Der erste Schritt des Verschiebevorgangs besteht darin, das Zielsubnetz für die Bereitstellung vorzubereiten. Dies kann einige Minuten dauern. Sobald das Subnetz bereit ist, wird der Verwaltungsvorgang zum Verschieben der Instanz gestartet und wird im Azure-Portal sichtbar. 


> [!div class="mx-imgBorder"]
> ![Auswählen des Subnetzes auf dem Blatt „Netzwerk“von SQL Managed Instance](./media/vnet-subnet-move-instance/how-to-select-subnet.png)


Überwachen Sie Instanzverschiebevorgänge auf dem Blatt **Übersicht** des Azure-Portals. Wählen Sie die Benachrichtigung aus, um ein zusätzliches Blatt mit Informationen zum aktuellen Schritt, den Gesamtschritten und einer Schaltfläche zum Abbrechen des Vorgangs zu öffnen. 

> [!div class="mx-imgBorder"]
> ![Überwachen des Instanzverschiebevorgangs](./media/vnet-subnet-move-instance/monitor-subnet-move-operation.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie den Azure PowerShell-Befehl [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance), um eine Instanz zu verschieben, nachdem Sie Ihr Subnetz in demselben virtuellen Netzwerk wie Ihr Zielsubnetz erstellt haben. Wenn Sie ein vorhandenes Subnetz verwenden möchten, geben Sie diesen Subnetznamen im PowerShell-Befehl an.

Die PowerShell-Beispielbefehle in diesem Abschnitt bereiten das Zielsubnetz für die Instanzbereitstellung vor und verschieben die verwaltete Instanz. 


Verwenden Sie den folgenden PowerShell-Befehl, um Ihre Parameter anzugeben: 

```powershell-interactive
### PART 1 - DEFINE PARAMETERS

#Generating basic parameters
$currentSubscriptionID = 'subscription-id'
$sqlMIResourceGroupName = 'resource-group-name-of-sql-mi'
$sqlMIName = 'sql-mi-name'
$sqlMIResourceVnetName = 'vnet-name-of-sql-mi'
$destinationSubnetName = 'name-of-the-destination-subnet-for-sql-mi'
```

Überspringen Sie diesen Befehl, wenn in Ihrem Subnetz bereits Instanzen bereitgestellt wurden. Wenn Sie ein neues Subnetz verwenden, verwenden Sie den folgenden Azure PowerShell-Befehl, um Ihr Subnetz vorzubereiten: 

```powershell-interactive
### PART 2 - PREPARE DESTINATION SUBNET

#Loading the url of script used for preparing the subnet for SQL MI deployment
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

#Generating destination subnet parameters
$parameters = @{
    subscriptionId = $currentSubscriptionID
    resourceGroupName = $sqlMIResourceGroupName
    virtualNetworkName = $sqlMIResourceVnetName
    subnetName = $destinationSubnetName
}

#Initiating subnet prepartion script
Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

> [!Note]
> Weitere Informationen zum Skript, das das Subnetz vorbereitet, finden Sie unter [Konfigurieren eines vorhandenen virtuellen Netzwerks für Azure SQL Managed Instance](vnet-existing-add-subnet.md). 

Der folgende Azure PowerShell-Befehl verschiebt die Instanz in das Quellsubnetz: 

```powershell-interactive
### PART 3 - MOVE INSTANCE TO THE NEW SUBNET

Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName"
```

Der folgende Azure PowerShell-Befehl verschiebt die -Instanz und bietet auch eine Möglichkeit, den Status zu überwachen: 

```powershell-interactive
###PART 3 EXTENDED - MOVE INSTANCE AND MONITOR PROGRESS

# Extend the Set-AzSqlInstance command with -AsJob -Force parameters to be able to monitor the progress or proceed with script execution as moving the instance to another subnet is long running operation 
Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName" -AsJob -Force

$operationProgress = Get-AzSqlInstanceOperation -ManagedInstanceName $sqlMIName -ResourceGroupName $sqlMIResourceGroupName
#checking the operation step status
Write-Host "Checking the ongoing step" -ForegroundColor Yellow
$operationProgress.OperationSteps.StepsList
```


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Azure CLI-Befehl [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update), um Ihre Instanz in ein anderes Subnetz zu verschieben. 

Geben Sie das Ziel an, indem Sie entweder die Subnetz-ID als `--subnet`-Eigenschaft oder den Namen des virtuellen Netzwerks als `--vnet-name`-Eigenschaft und den Subnetznamen als `--subnet`-Eigenschaft angeben. 

Im folgenden Beispiel wird die verwaltete Instanz durch Angeben der Subnetz-ID in ein anderes Subnetz verschoben: 


```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --subnet /subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVirtualNetworkName/subnets/destinationSubnetName
```

Im folgenden Beispiel wird die verwaltete Instanz durch Angeben des Namens des virtuellen Netzwerks und des Subnetznamens in ein anderes Subnetz verschoben:

```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --vnet-name myVirtualNetworkName --subnet destinationSubnetName
```

Verwenden Sie den folgenden Befehl, um den Status des Verwaltungsvorgangs zu überwachen: 

```azurecli-interactive
az sql mi op list -g myResourceGroup --mi mySqlManagedInstance
```
---

## <a name="next-steps"></a>Nächste Schritte

- Im [Schnellstarthandbuch](instance-create-quickstart.md) erfahren Sie, wie Sie Ihre erste verwaltete Instanz erstellen.
- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](../database/features-comparison.md).
- Weitere Informationen zur VNET-Konfiguration finden Sie unter [Konfigurieren eines VNET für SQL Managed Instance](connectivity-architecture-overview.md).
- Eine Schnellstartanleitung, in der eine verwaltete Instanz erstellt und eine Datenbank aus einer Sicherungsdatei wiederhergestellt wird, finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](instance-create-quickstart.md).
- Ein Tutorial zur Verwendung des Azure Database Migration Service für die Migration finden Sie unter [SQL Managed Instance-Migration mithilfe des Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
