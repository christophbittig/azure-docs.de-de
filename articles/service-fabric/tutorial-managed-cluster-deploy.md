---
title: Bereitstellen eines verwalteten Service Fabric-Clusters
description: In diesem Tutorial stellen Sie einen verwalteten Service Fabric-Cluster zu Testzwecken bereit.
ms.topic: tutorial
ms.date: 8/23/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 3117c4c248aa073fb961dc031342d8b3d9489bbd
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867402"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster"></a>Tutorial: Bereitstellen eines verwalteten Service Fabric-Clusters

In dieser Tutorialreihe wird Folgendes behandelt:

> [!div class="checklist"]
> * Bereitstellen eines verwalteten Service Fabric-Clusters 
> * [Aufskalieren eines verwalteten Service Fabric-Clusters](tutorial-managed-cluster-scale.md)
> * [Hinzufügen und Entfernen von Knoten in einem verwalteten Service Fabric-Cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [Bereitstellen einer Anwendung in einem verwalteten Service Fabric-Cluster](tutorial-managed-cluster-deploy-app.md)

In diesem Teil der Reihe wird Folgendes behandelt:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit Ihrem Azure-Konto
> * Erstellen einer neuen Ressourcengruppe
> * Bereitstellen eines verwalteten Service Fabric-Clusters
> * Hinzufügen eines primären Knotentyps zum Cluster

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch nicht über ein Azure-Abonnement verfügen.

* Installieren Sie das [Service Fabric SDK und das PowerShell-Modul](service-fabric-get-started.md).

* Installieren Sie [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (oder höher).

## <a name="connect-to-your-azure-account"></a>Herstellen einer Verbindung mit Ihrem Azure-Konto

Ersetzen Sie `<your-subscription>` durch die Verbindungszeichenfolge für Ihr Azure-Konto, und stellen Sie eine Verbindung her:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Erstellen einer neuen Ressourcengruppe

Erstellen Sie nun die Ressourcengruppe für den verwalteten Service Fabric-Cluster, und ersetzen Sie `<your-rg>` und `<location>` durch den gewünschten Gruppennamen und Speicherort.

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Bereitstellen eines verwalteten Service Fabric-Clusters

### <a name="create-a-service-fabric-managed-cluster"></a>Erstellen eines verwalteten Service Fabric-Clusters

In diesem Schritt erstellen Sie mithilfe des PowerShell-Befehls New-AzServiceFabricManagedCluster einen verwalteten Service Fabric-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen myCluster in der Ressourcengruppe mit dem Namen myResourceGroup erstellt. Diese Ressourcengruppe wurde im vorherigen Schritt in der Region „eastus2“ erstellt.

Geben Sie für diesen Schritt Ihre eigenen Werte für die folgenden Parameter an:

* **Clustername**: Geben Sie einen eindeutigen Namen für Ihren Cluster ein, z. B. *mysfcluster*.
* **Administratorkennwort**: Geben Sie ein Kennwort für den Administrator ein, das für RDP für die zugrunde liegenden VMs im Cluster verwendet werden soll.
* **Clientzertifikat-Fingerabdruck**: Geben Sie den Fingerabdruck des Clientzertifikats an, das Sie für den Zugriff auf Ihren Cluster verwenden möchten. Wenn Sie über kein Zertifikat verfügen, befolgen Sie die Anleitungen unter [Festlegen und Abrufen eines Zertifikats](../key-vault/certificates/quick-create-portal.md), um ein selbstsigniertes Zertifikat zu erstellen.
* **Cluster-SKU**: Geben Sie den [Typ des bereitzustellenden verwalteten Service Fabric-Clusters](overview-managed-cluster.md#service-fabric-managed-cluster-skus) an. Cluster der SKU *Basic* sind nur für Testbereitstellungen vorgesehen und lassen das Hinzufügen oder Entfernen von Knotentypen nicht zu.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Hinzufügen eines primären Knotentyps zum verwalteten Service Fabric-Cluster

In diesem Schritt fügen Sie dem soeben erstellten Cluster einen primären Knotentyp hinzu. Jeder Service Fabric-Cluster muss über mindestens einen primären Knotentyp verfügen.

Geben Sie für diesen Schritt Ihre eigenen Werte für die folgenden Parameter an:

* **Name des Knotentyps**: Geben Sie einen eindeutigen Namen für den Knotentyp ein, der dem Cluster hinzugefügt werden soll, z. B. „NT1“.

> [!NOTE]
> Wenn der hinzugefügte Knotentyp der erste oder einzige Knotentyp im Cluster ist, muss die Eigenschaft „Primary“ verwendet werden.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

### <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Nachdem die Bereitstellung abgeschlossen ist, finden Sie den Service Fabric Explorer-Wert auf der Übersichtsseite der verwalteten Service Fabric-Clusterressource im Portal. Wenn Sie zur Eingabe eines Zertifikats aufgefordert werden, verwenden Sie das Zertifikat, für das der Clientfingerabdruck im PowerShell-Befehl bereitgestellt wurde.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schritt haben wir den ersten verwalteten Service Fabric-Cluster erstellt und bereitgestellt. Weitere Informationen zum Skalieren eines Clusters finden Sie unter:

> [!div class="nextstepaction"]
> [Aufskalieren eines verwalteten Service Fabric-Clusters](tutorial-managed-cluster-scale.md)
