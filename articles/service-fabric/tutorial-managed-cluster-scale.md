---
title: Aufskalieren eines verwalteten Service Fabric-Clusters
description: In diesem Tutorial erfahren Sie, wie Sie Knotentypen eines verwalteten Service Fabric-Clusters aufskalieren.
ms.topic: tutorial
ms.date: 8/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 938c163f4b27706999f7fe32f7935d4d7877c96b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864968"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster"></a>Tutorial: Aufskalieren eines verwalteten Service Fabric-Clusters

In dieser Tutorialreihe wird Folgendes behandelt:

> [!div class="checklist"]
> * [Bereitstellen eines verwalteten Service Fabric-Clusters.](tutorial-managed-cluster-deploy.md)
> * Aufskalieren eines verwalteten Service Fabric-Clusters
> * [Hinzufügen und Entfernen von Knoten in einem verwalteten Service Fabric-Cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [Bereitstellen einer Anwendung in einem verwalteten Service Fabric-Cluster](tutorial-managed-cluster-deploy-app.md)

In diesem Teil der Reihe wird Folgendes behandelt:

> [!div class="checklist"]
> * Skalieren eines verwalteten Service Fabric-Clusterknotens

## <a name="prerequisites"></a>Voraussetzungen

* Ein verwalteter Service Fabric-Cluster (siehe [*Bereitstellen eines verwalteten Clusters*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) oder höher (siehe [*Installieren von Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Skalieren eines verwalteten Service Fabric-Clusters
Ändern Sie die Instanzanzahl, um die Anzahl der Knoten für den Knotentyp zu erhöhen oder zu verringern, die Sie skalieren möchten. Sie finden Knotentypnamen in der ARM-Vorlage (Azure Resource Manager) aus der Clusterbereitstellung oder in Service Fabric Explorer.  

> [!NOTE]
> Für den primären Knotentyp können Sie für einen Cluster der SKU „Basic“ nicht unter 3 Knoten und für einen Cluster der SKU „Standard“ nicht unter 5 Knoten skalieren.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Das Upgrade des Clusters wird automatisch ausgeführt, und nach einigen Minuten werden die zusätzlichen Knoten angezeigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schritt haben wir einen Knotentyp auf einem verwalteten Service Fabric-Cluster skaliert. Weitere Informationen zum Hinzufügen und Entfernen von Knotentypen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Hinzufügen und Entfernen von Knoten in einem verwalteten Service Fabric-Cluster](tutorial-managed-cluster-add-remove-node-type.md)
