---
title: Herstellen einer Verbindung mit einem verwalteten Service Fabric-Cluster
description: Hier erfahren Sie, wie Sie eine Verbindung mit einem verwalteten Service Fabric-Cluster herstellen.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aa168a283be909678a199eee887d69bb3eb2024a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021524"
---
# <a name="connect-to-a-service-fabric-managed-cluster"></a>Herstellen einer Verbindung mit einem verwalteten Service Fabric-Cluster

Nachdem Sie einen Cluster über das [Portal, eine Azure Resource Manager-Vorlage](quickstart-managed-cluster-template.md) oder [PowerShell](tutorial-managed-cluster-deploy.md) bereitgestellt haben, gibt es verschiedene Möglichkeiten, eine Verbindung mit Ihrem verwalteten Cluster herzustellen und diesen anzuzeigen. 

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals

Navigieren Sie wie folgt zu Ihrer verwalteten Clusterressource:

 1) Navigieren Sie zu https://portal.azure.com/.

 2) Navigieren Sie zu Ihrer Clusterressource, indem Sie nach Service Fabric suchen und „Verwaltete Service Fabric-Cluster“ auswählen.

 3) Auswählen Ihres Clusters

 4) Auf dieser Benutzeroberfläche können Sie bestimmte Parameter anzeigen und ändern. Weitere Informationen zu den verfügbaren Optionen finden Sie unter [Konfigurationsoptionen für verwaltete Service Fabric-Cluster](how-to-managed-cluster-configuration.md).

## <a name="use-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

[Service Fabric Explorer](https://github.com/Microsoft/service-fabric-explorer) (SFX) ist eine Anwendung zum Untersuchen und Verwalten der Anwendungs- und Clusterintegrität eines Microsoft Azure Service Fabric-Clusters. 

So navigieren Sie zu SFX für Ihren verwalteten Cluster
 
 1) Navigieren Sie zu https://portal.azure.com/.
 
 2) Navigieren Sie zu Ihrer Clusterressource, indem Sie nach Service Fabric suchen und „Verwaltete Service Fabric-Cluster“ auswählen.

 3) Auswählen Ihres Clusters

 4) Navigieren Sie oben rechts zu `SF Explorer`. Beispiel: https://mycluster.region.cloudapp.azure.com:19080/Explorer

## <a name="use-powershell-modules"></a>Verwenden von PowerShell-Modulen

Die folgenden PowerShell-Module stehen zum Verbinden, Anzeigen und Ändern von Konfigurationen für Ihren Cluster zur Verfügung: 

* Installieren Sie das [Service Fabric SDK und das PowerShell-Modul](service-fabric-get-started.md).

* Installieren Sie [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (oder höher).

### <a name="using-the-service-fabric-powershell-module"></a>Verwenden des Service Fabric-PowerShell-Moduls
Um dieses Modul verwenden zu können, benötigen Sie den Clusterzertifikatfingerabdruck. Sie finden diesen Wert in der Ausgabe der Clustereigenschaften Ihrer Ressourcenbereitstellung oder durch Abfragen der Clustereigenschaften für eine vorhandene Ressource.

Der folgende Befehl kann verwendet werden, um die Clusterressource nach dem Clusterzertifikat-Fingerabdruck abzufragen.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprints
```

Sobald Sie über den Clusterzertifikat-Fingerabdruck verfügen, können Sie eine Verbindung mit Ihrem Cluster herstellen.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="using-the-azure-service-fabric-powershell-module"></a>Verwenden des Service Fabric-PowerShell-Moduls

Mit dem Azure Service Fabric-Modul können Sie Vorgänge wie das Erstellen eines verwalteten Clusters, das Skalieren eines Knotentyps und das Anzeigen von Informationen für verwaltete Clusterressourcen durchführen. Die für verwaltete Cluster unterstützten Cmdlets heißen `AzServiceFabricManagedCluster*`. Informationen dazu finden Sie in der Dokumentation zum [PowerShell-Modul „Az.ServiceFabric“](/powershell/module/az.servicefabric/).


Im folgenden Beispiel wird eines der Cmdlets verwendet, um die Details eines verwalteten Clusters anzuzeigen:

```powershell
$rgName = "testResourceGroup"
$clusterName = "mycluster"
Get-AzServiceFabricManagedCluster -ResourceGroupName $rgName -Name $clusterName
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Anwendung in einem verwalteten Cluster mithilfe von Azure Resource Manager](how-to-managed-cluster-app-deployment-template.md)
* [Konfigurieren von Optionen für verwaltete Cluster](how-to-managed-cluster-configuration.md)



