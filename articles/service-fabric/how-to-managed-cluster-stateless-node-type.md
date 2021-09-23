---
title: Bereitstellen eines verwalteten Service Fabric-Clusters mit zustandslosen Knotentypen
description: Erfahren Sie, wie Sie zustandslose Knotentypen in Azure Service Fabric-Clustern erstellen und bereitstellen.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 1d6cd328a195e35bd2523fa24aed9153267bd83e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867336"
---
# <a name="deploy-a-service-fabric-managed-cluster-with-stateless-node-types"></a>Bereitstellen eines verwalteten Service Fabric-Clusters mit zustandslosen Knotentypen

Für Service Fabric-Knotentypen gilt die Annahme, dass zu einem bestimmten Zeitpunkt ggf. zustandsbehaftete Dienste auf den Knoten platziert werden. Zustandslose Knotentypen lockern diese Annahme für einen Knotentyp. Durch Lockerung dieser Annahme können zustandslose Knotentypen von schnellerer horizontaler Skalierung profitieren, indem einige der Einschränkungen für Reparatur- und Wartungsvorgänge aufgehoben werden.

* Primäre Knotentypen können nicht als zustandslos konfiguriert werden.
* Zustandslose Knotentypen erfordern eine API-Version von **2021-05-01** oder höher.
* Dadurch wird die Eigenschaft **multipleplacementgroup** automatisch auf **true** festgelegt, über die Sie [hier mehr erfahren](how-to-managed-cluster-large-virtual-machine-scale-sets.md) können.
* Dies ermöglicht Unterstützung von bis zu 1.000 Knoten für den angegebenen Knotentyp.

Es sind Beispielvorlagen verfügbar: [Vorlage für zustandslose Service Fabric-Knotentypen](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enable-stateless-node-types-in-a-service-fabric-managed-cluster"></a>Aktivieren von zustandslosen Knotentypen in einem verwalteten Service Fabric-Clusters
Legen Sie die **isStateless**-Eigenschaft auf **TRUE** fest, um mindestens einen Knotentyp in einer Knotentypressource als zustandslos festzulegen. Beim Bereitstellen eines Service Fabric-Clusters mit zustandslosen Knotentypen ist mindestens ein primärer Knotentyp erforderlich, der im Cluster nicht zustandslos ist.

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
                "isStateless": true,
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
}
```

## <a name="configure-stateless-node-types-with-multiple-availability-zones"></a>Konfigurieren zustandsloser Knotentypen mit mehreren Verfügbarkeitszonen
Um einen zustandslosen Knotentyp zu konfigurieren, der sich über mehrere Verfügbarkeitszonen erstreckt, befolgen Sie die Anleitungen unter [Verfügbarkeitszonenübergreifende Service Fabric-Cluster](.\service-fabric-cross-availability-zones.md). 

>[!NOTE]
> Die Eigenschaft für zonale Resilienz muss auf Clusterebene festgelegt werden, und diese Eigenschaft kann nicht an Ort und Stelle geändert werden.

## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrieren zur Verwendung zustandsloser Knotentypen in einem Cluster
Für alle Migrationsszenarien muss ein neuer, zustandsloser Knotentyp hinzugefügt werden. Der vorhandene Knotentyp kann nicht als zustandslos migriert werden. Sie können einem vorhandenen verwalteten Service Fabric-Cluster einen neuen zustandslosen Knotentyp hinzufügen und alle ursprünglichen Knotentypen aus dem Cluster entfernen. 

## <a name="next-steps"></a>Nächste Schritte 

Weitere Informationen zu verwalteten Service Fabric-Clustern finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen zu verwalteten Service Fabric-Clustern](./faq-managed-cluster.yml)