---
title: Bereitstellen einer Anwendung in einem verwalteten Service Fabric-Cluster mit PowerShell
description: In diesem Tutorial stellen Sie eine Verbindung mit einem verwalteten Service Fabric-Cluster her und stellen eine Anwendung mit PowerShell bereit.
ms.topic: tutorial
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b1228deaf5627c8a367a9b0927320f93d98a50e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867438"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster"></a>Tutorial: Bereitstellen einer App in einem verwalteten Service Fabric-Cluster

In dieser Tutorialreihe wird Folgendes behandelt:

> [!div class="checklist"]
> * [Bereitstellen eines verwalteten Service Fabric-Clusters](tutorial-managed-cluster-deploy.md)
> * [Aufskalieren eines verwalteten Service Fabric-Clusters](tutorial-managed-cluster-scale.md)
> * [Hinzufügen und Entfernen von Knoten in einem verwalteten Service Fabric-Cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * Bereitstellen einer Anwendung in einem verwalteten Service Fabric-Cluster

In diesem Teil der Reihe wird Folgendes behandelt:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit einem verwalteten Service Fabric-Cluster
> * Hochladen einer Anwendung in einen Cluster
> * Instanziieren einer Anwendung in einem Cluster
> * Entfernen einer Anwendung aus einem Cluster

## <a name="prerequisites"></a>Voraussetzungen

* Ein verwalteter Service Fabric-Cluster (siehe [*Bereitstellen eines verwalteten Clusters*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Herstellen einer Clusterverbindung

Zum Herstellen einer Verbindung mit Ihrem Cluster benötigen Sie den Clusterzertifikat-Fingerabdruck. Sie finden diesen Wert in der Ausgabe der Clustereigenschaften Ihrer Ressourcenbereitstellung oder durch Abfragen der Clustereigenschaften für eine vorhandene Ressource.

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

### <a name="upload-an-application-package"></a>Hochladen eines Anwendungspakets

In diesem Tutorial verwenden wir das Beispiel [Service Fabric Voting-Anwendung](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy). Weitere Informationen zur Service Fabric-Anwendungsbereitstellung mit PowerShell finden Sie unter [Bereitstellen und Entfernen von Service Fabric-Anwendungen](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> In der Vorschau des verwalteten Service Fabric-Clusters können Sie Anwendungen nicht direkt aus Visual Studio veröffentlichen.

Zuerst müssen Sie die [Anwendung für die Bereitstellung verpacken](service-fabric-package-apps.md). Führen Sie für dieses Tutorial die Schritte zum Verpacken einer Anwendung aus Visual Studio aus. Es ist wichtig, dass Sie sich den Pfad notieren, in dem die Anwendung verpackt wurde, da er für den weiter unten benötigten Pfad verwendet wird.

Nachdem das Anwendungspaket erstellt wurde, können Sie das Anwendungspaket in Ihren Cluster hochladen. Aktualisieren Sie den `$path`-Wert, um den Pfad für das Anwendungspaket darzustellen, und führen Sie Folgendes aus:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Erstellen einer Anwendung

Sie können eine Anwendung mit einer beliebigen Version des Anwendungstyps instanziieren, die mit dem Cmdlet New-ServiceFabricApplication erfolgreich registriert wurde. Der Name jeder Anwendung muss mit dem „fabric:“ -Schema beginnen und für jede Anwendungsinstanz eindeutig sein. Wenn im Anwendungsmanifest des Zielanwendungstyps Standarddienste festgelegt wurden, werden diese ebenfalls erstellt.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Sobald dieser Vorgang abgeschlossen ist, sollten die Anwendungsinstanzen in Service Fabric Explorer ausgeführt werden.

### <a name="remove-an-application"></a>Entfernen einer Anwendung

Wenn eine Anwendungsinstanz nicht mehr benötigt wird, können Sie sie mit dem Cmdlet `Remove-ServiceFabricApplication` dauerhaft entfernen, wodurch auch automatisch alle Dienste entfernt werden, die zu der Anwendung gehören, sodass der gesamte Dienstzustand dauerhaft entfernt wird.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schritt haben wir eine Anwendung in einem verwalteten Service Fabric-Cluster bereitgestellt. Weitere Informationen zu den Optionen für die Anwendungsbereitstellung finden Sie unter:

* [Bereitstellen von Anwendungsgeheimnissen in einem verwalteten Cluster](how-to-managed-cluster-application-secrets.md)
* [Bereitstellen einer Anwendung in einem verwalteten Cluster mithilfe von Azure Resource Manager](how-to-managed-cluster-app-deployment-template.md)
* [Bereitstellen einer Anwendung mit verwalteter Identität in einem verwalteten Cluster](how-to-managed-cluster-application-managed-identity.md)


Weitere Informationen zu Konfigurationsoptionen für verwaltete Cluster finden Sie unter:

* [Konfigurieren des verwalteten Clusters](how-to-managed-cluster-configuration.md)
