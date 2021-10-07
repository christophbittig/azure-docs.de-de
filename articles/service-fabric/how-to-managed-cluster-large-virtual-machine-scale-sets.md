---
title: Konfigurieren eines sekundären Knotentyps für große VM-Skalierungsgruppen in einem verwalteten Service Fabric-Cluster
description: In diesem Artikel wird beschrieben, wie Sie einen sekundären Knotentyp als große VM-Skalierungsgruppe konfigurieren.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 05177b519caa504ac9d931b07b9f6126a3851c0b
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545094"
---
# <a name="service-fabric-managed-cluster-node-type-scaling"></a>Skalieren eines verwalteten Service Fabric-Clusterknotentyps

Jeder Knotentyp in einem verwalteten Service Fabric-Cluster wird von einer VM-Skalierungsgruppe unterstützt. Damit verwaltete Clusterknotentypen [große VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) erstellen können, wurde der Knotentypdefinition eine Eigenschaft `multiplePlacementGroups` hinzugefügt. Standardmäßig legen verwaltete Clusterknotentypen diese Eigenschaft auf FALSE fest, um die Fehler- und UpgradedDomänen innerhalb einer Platzierungsgruppe konsistent zu halten, aber diese Einstellung schränkt die Skalierung eines Knotentyps auf maximal 100 VMs ein. Um zu entscheiden, ob Ihre Anwendung große Skalierungsgruppen effektiv nutzen kann, sehen Sie sich [diese Liste mit Anforderungen](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md#checklist-for-using-large-scale-sets) an.

Da der Azure Service Fabric-Ressourcenanbieter für verwaltete Cluster die Skalierung orchestriert und verwaltete Datenträger für Daten verwendet, können wir große Skalierungsgruppen sowohl für zustandsbehaftete als auch für zustandslose sekundäre Knotentypen unterstützen.

> [!NOTE]
> Diese Eigenschaft kann nach der Bereitstellung eines Knotentyps nicht mehr geändert werden.

## <a name="enable-large-virtual-machine-scale-sets-in-a-service-fabric-managed-cluster"></a>Aktivieren großer VM-Skalierungsgruppen in einem verwalteten Service Fabric-Cluster
Um einen sekundären Knotentyp als große Skalierungsgruppe zu konfigurieren, legen Sie die Eigenschaft **multiplePlacementGroups** auf **true** fest.
> [!NOTE]
> Diese Eigenschaft kann nicht für den primären Knotentyp festgelegt werden.

* Die apiVersion der verwalteten Service Fabric-Clusterressource sollte **2021-05-01** oder höher lauten.

```json
{
  "apiVersion": "[variables('sfApiVersion')]",
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
  "location": "[resourcegroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
  ],
  "properties": {
    "multiplePlacementGroups": true,
    "isPrimary": false,
    "vmImagePublisher": "[parameters('vmImagePublisher')]",
    "vmImageOffer": "[parameters('vmImageOffer')]",
    "vmImageSku": "[parameters('vmImageSku')]",
    "vmImageVersion": "[parameters('vmImageVersion')]",
    "vmSize": "[parameters('nodeTypeSize')]",
    "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
    "dataDiskSizeGB": "[parameters('nodeTypeDataDiskSizeGB')]"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen einer App in einem verwalteten Service Fabric-Cluster](./tutorial-managed-cluster-deploy-app.md)
