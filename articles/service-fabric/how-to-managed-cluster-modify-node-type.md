---
title: Konfigurieren oder ändern Sie einen von Service Fabric verwalteten Clusterknotentyp
description: Dieser Artikel beschreibt, wie Sie einen verwalteten Clusterknotentyp ändern können
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 02d332774b98e7097bca0bbea6bc7216af0057fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277256"
---
# <a name="service-fabric-managed-cluster-node-types"></a>Service Fabric verwaltete Clusterknotentypen

Jeder Knotentyp in einem verwalteten Service Fabric-Cluster wird von einer VM-Skalierungsgruppe unterstützt. Bei verwalteten Clustern nehmen Sie alle erforderlichen Änderungen über den Ressourcenanbieter für verwaltete Cluster der Service Fabric vor. Alle dem Cluster zugrundeliegenden Ressourcen werden vom Anbieter des verwalteten Clusters in Ihrem Namen erstellt und abstrahiert. Die Verwaltung der Ressourcen durch den Ressourcenanbieter vereinfacht die Bereitstellung und Verwaltung des Clusterknotentyps, verhindert Bedienungsfehler wie das Löschen eines Seed-Knotens und die Anwendung bewährter Verfahren wie die Überprüfung der sicheren Verwendung einer VM-SKU.

Der Rest dieses Dokuments befasst sich mit der Anpassung verschiedener Einstellungen, von der Erstellung eines Knotentyps über die Anpassung der Anzahl der Instanzen des Knotentyps, die Aktivierung automatischer Betriebssystem-Image-Upgrades, die Änderung des Betriebssystem-Images und die Konfiguration der Platzierungseigenschaften. In diesem Dokument geht es auch um die Verwendung von Azure-Portal oder Azure Resource Manager Templates, um Änderungen vorzunehmen.

> [!NOTE]
> Sie können den Knotentyp nicht ändern, solange eine Änderung im Gange ist. Es wird empfohlen, eine angeforderte Änderung erst abzuschließen, bevor eine weitere vorgenommen wird.


## <a name="add-or-remove-a-node-type-with-portal"></a>Hinzufügen oder Entfernen eines Knotentyps mit Ausschnitt

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie einen Knotentyp mithilfe des Ausschnitts hinzufügen oder entfernen können.

**Zum Hinzufügen eines Knotentyps:**
1) Anmeldung beim [Azure-Portal](https://portal.azure.com/)

2) Navigieren Sie zur Übersichtsseite Ihrer Cluster-Ressourcen. 
![Musterübersichtsseite][overview]

3) Wählen Sie `Node types` unter dem `Settings` Abschnitt ![Knotentypenansicht][addremove]

4) Klicken Sie oben auf `Add`, geben Sie die erforderlichen Informationen ein, und klicken Sie dann unten auf Hinzufügen.


**So entfernen Sie einen Knotentyp:**
1) Anmeldung beim [Azure-Portal](https://portal.azure.com/)

2) Navigieren Sie zur Übersichtsseite Ihrer Cluster-Ressourcen. 
![Musterübersichtsseite][overview]

3) Wählen Sie `Node types` unter dem `Settings` Abschnitt ![Knotentypenansicht][addremove]

4) Wählen Sie die `Node Type`, die Sie entfernen möchten, und klicken Sie oben auf `Delete`.

> [!NOTE]
> Es ist nicht möglich, einen primären Knotentyp zu entfernen, wenn er der einzige primäre Knotentyp im Cluster ist.


## <a name="add-a-node-type-with-a-template"></a>Hinzufügen eines Knotentyps mit einer Vorlage

**Zum Hinzufügen eines Knotentyps mit einer ARM-Vorlage**

Fügen Sie einen weiteren Ressourcentyp `Microsoft.ServiceFabric/managedclusters/nodetypes` mit den erforderlichen Werten hinzu und führen Sie eine Clusterbereitstellung durch, damit die Einstellung wirksam wird.

* Die apiVersion der verwalteten Service Fabric-Clusterressource sollte **2021-05-01** oder höher lauten.

```json
          {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeType2Name'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeType2VmSize')]",
                "vmInstanceCount": "[parameters('nodeType2VmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeType2DataDiskSizeGB')]",
                "dataDiskType": "[parameters('nodeType2managedDataDiskType')]"
           }
```
Ein Beispiel für eine Zwei-Knoten-Typ-Konfiguration finden Sie in unserer [Beispiel-ARM-Vorlage für zwei Knoten](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/SF-Managed-Standard-SKU-2-NT)


## <a name="scale-a-node-type-manually-with-portal"></a>Einen Knotentyp manuell mit dem Ausschnitt skalieren

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie die Knotenanzahl für einen Knotentyp mithilfe des Ausschnitts ändern können.

1) Anmeldung beim [Azure-Portal](https://portal.azure.com/)

2) Navigieren Sie zur Übersichtsseite Ihrer Cluster-Ressourcen. 
![Musterübersichtsseite][overview]

3) Wählen Sie `Node Types` unter dem Abschnitt `Settings` 

4) Wählen Sie die `Node type name`, die Sie ändern möchten

5) Stellen Sie `Node count` auf den gewünschten neuen Wert ein und wählen Sie unten `Apply`. In diesem Bildschirmfoto war der Wert `3` und wurde auf `5` angepasst.
![Beispiel für eine Erhöhung der Knotenanzahl][adjust-node-count]

6) Die `Provisioning state` zeigt nun bis zur Fertigstellung den Status `Updating` an. Nach Abschluss wird wieder `Succeeded` angezeigt.
![Beispiel für die Aktualisierung eines Knotentyps][node-type-updating]


## <a name="scale-a-node-type-manually-with-a-template"></a>Einen Knotentyp manuell mit einer Vorlage skalieren

Um die Knotenanzahl für einen Knotentyp mit einer ARM-Vorlage anzupassen, passen Sie die Eigenschaft `vmInstanceCount` mit dem neuen Wert an und führen eine Clusterbereitstellung durch, damit die Einstellung wirksam wird.

* Die apiVersion der verwalteten Service Fabric-Clusterressource sollte **2021-05-01** oder höher lauten.

> [!NOTE]
> Der Anbieter des verwalteten Clusters blockiert Skalierungsanpassungen und gibt einen Fehler zurück, wenn die Skalierungsanforderung die erforderlichen Mindestwerte verletzt.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                ...
            }
        }
}
```

## <a name="enable-automatic-os-image-upgrades"></a>Aktivieren automatischer Betriebssystemupgrades

Sie können automatische Betriebssystemimage-Upgrades für die virtuellen Computer aktivieren, auf denen Ihre verwalteten Clusterknoten ausgeführt werden. Obwohl die VM-Skalierungsgruppenressourcen in Ihrem Auftrag mit verwalteten Service Fabric-Clustern verwaltet werden, können Sie automatische Betriebssystemimage-Upgrades für Ihre Clusterknoten aktivieren. Wie [klassische Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration)-Cluster werden verwaltete Clusterknoten nicht standardmäßig aktualisiert, um unbeabsichtigte Störungen Ihres Clusters zu verhindern.

So aktivieren Sie automatische Betriebssystemupgrades:

* Verwenden Sie die Version `2021-05-01` (oder höher) von *Microsoft.ServiceFabric/managedclusters*- und *Microsoft.ServiceFabric/managedclusters/nodetypes*-Ressourcen.
* Setzen Sie die Eigenschaft `enableAutoOSUpgrade` des Clusters auf *TRUE*.
* Setzen Sie die nodeTypes-Ressourceneigenschaft `vmImageVersion` des Clusters auf *latest*.

Beispiel:

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Nach der Aktivierung beginnt Service Fabric mit der Abfrage und Nachverfolgung von Betriebssystemimage-Versionen im verwalteten Cluster. Wenn eine neue Betriebssystemversion verfügbar ist, werden die Clusterknotentypen (VM-Skalierungsgruppen) nacheinander aktualisiert. Service Fabric-Runtimeupgrades werden erst ausgeführt, nachdem feststeht, dass derzeit keine Betriebssystemimage-Upgrades für Clusterknoten ausgeführt werden.

Wenn bei einem Upgrade ein Fehler auftritt, versucht Service Fabric es nach 24 Stunden erneut. Maximal drei erneute Versuche werden unternommen. Ähnlich wie bei klassischen (nicht verwalteten) Service Fabric-Upgrades können fehlerhafte Apps oder Knoten das Betriebssystemimage-Upgrade blockieren.

Weitere Informationen zu Imageupgrades finden Sie unter [Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="modify-the-os-image-for-a-node-type-with-portal"></a>Ändern Sie das Betriebssystem-Image für einen Knotentyp mit Portal

In dieser Schritt-für-Schritt-Anleitung erfahren Sie, wie Sie das Betriebssystem-Image für einen Knotentyp mithilfe des Portals ändern können.

1) Anmeldung beim [Azure-Portal](https://portal.azure.com/)

2) Navigieren Sie zur Übersichtsseite Ihrer Cluster-Ressourcen. 
![Musterübersichtsseite][overview]

3) Wählen Sie `Node Types` unter dem Abschnitt `Settings` 

4) Wählen Sie die `Node type name`, die Sie ändern möchten

5) Stellen Sie `OS Image` auf den gewünschten neuen Wert ein und wählen Sie unten `Apply`. ![Beispiel zum Ändern des Betriebssystem-Images][change-os-image]

6) Die `Provisioning state` zeigt nun den Status `Updating` an und fährt mit einer Upgrade-Domäne nach der anderen fort. Nach Abschluss wird wieder `Succeeded` angezeigt.
![Beispiel für die Aktualisierung eines Knotentyps][node-type-updating]


## <a name="modify-the-os-image-for-a-node-type-with-a-template"></a>Ändern Sie das Betriebssystem-Image für einen Knotentyp mit einer Vorlage

Um das für einen Knotentyp verwendete Betriebssystem-Image mithilfe einer ARM-Vorlage zu ändern, passen Sie die Eigenschaft `vmImageSku` mit dem neuen Wert an und führen Sie eine Cluster-Bereitstellung durch, damit die Einstellung wirksam wird. Der Anbieter des verwalteten Clusters führt ein Re-Image jeder Instanz nach Upgrade-Domäne durch.

* Die apiVersion der verwalteten Service Fabric-Clusterressource sollte **2021-05-01** oder höher lauten.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                ...
            }
        }
}
```

## <a name="configure-placement-properties-for-a-node-type-with-portal"></a>Konfigurieren Sie die Platzierungseigenschaften für einen Knotentyp mit Ausschnitt

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie eine Platzierungseigenschaft für einen Knotentyp mithilfe eines Ausschnitts ändern können.

1) Anmeldung beim [Azure-Portal](https://portal.azure.com/)

2) Navigieren Sie zur Übersichtsseite Ihrer Cluster-Ressourcen. 
![Musterübersichtsseite][overview]

3) Wählen Sie `Node Types` unter dem Abschnitt `Settings` 

4) Wählen Sie die `Node type name`, die Sie ändern möchten

5) Geben Sie im Abschnitt `Placement properties` den gewünschten Namen und Wert ein und wählen Sie unten `Apply`. In diesem Screenshot wurde die `Name` `SSD_Premium` wurde mit `Value` von `true` verwendet.
![Beispiel für das Hinzufügen einer Platzierungseigenschaft][nodetype-placement-property]

6) Die `Provisioning state` zeigt nun bis zur Fertigstellung den Status `Updating` an. Nach Abschluss wird wieder `Succeeded` angezeigt.
![Beispiel für die Aktualisierung eines Knotentyps][node-type-updating]

Sie können nun diese [Platzeigenschaft verwenden, um sicherzustellen, dass bestimmte Arbeitslasten nur auf bestimmten Knotentypen im Cluster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) ausgeführt werden. 

## <a name="configure-placement-properties-for-a-node-type-with-a-template"></a>Konfigurieren der Platzierungseigenschaften für einen Knotentyp mit einer Vorlage

Um die Platzierungseigenschaften für einen Knotentyp mithilfe einer ARM-Vorlage anzupassen, passen Sie die Eigenschaft `placementProperties` mit den neuen Werten an und führen Sie eine Clusterbereitstellung durch, damit die Einstellung wirksam wird. Das folgende Beispiel zeigt, wie drei Werte für einen Knotentyp festgelegt werden.

* Die apiVersion der verwalteten Service Fabric-Clusterressource sollte **2021-05-01** oder höher lauten.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "placementProperties": {
                    "PremiumSSD": "true",
                    "NodeColor": "green",
                    "SomeProperty": "5"
            }
        }
}
```
Sie können nun diese [Platzeigenschaft verwenden, um sicherzustellen, dass bestimmte Arbeitslasten nur auf bestimmten Knotentypen im Cluster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) ausgeführt werden. 

## <a name="modify-the-vm-sku-for-a-node-type"></a>Ändern Sie die VM SKU für einen Knotentyp

Service Fabric Managed Cluster unterstützt keine In-Place-Änderung der VM SKU, ist aber einfacher als die klassische Variante. Um dies zu erreichen, müssen Sie Folgendes tun:
* [Erstellen Sie einen neuen Knotentyp](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal) mit der erforderlichen VM SKU.
* Migrieren Sie Ihre Arbeitslast. Eine Möglichkeit besteht darin, eine [Platzeigenschaft zu verwenden, um sicherzustellen, dass bestimmte Arbeitslasten nur auf bestimmten Arten von Knoten im Cluster](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) laufen. 
* [Alten Knotentyp löschen](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)



## <a name="configure-multiple-managed-disks-preview"></a>Konfigurieren Sie mehrere verwaltete Datenträger (Vorschau)
Service Fabric verwaltete Cluster konfigurieren standardmäßig einen verwalteten Datenträger. Durch die Konfiguration der folgenden optionalen Eigenschaften und Werte können Sie mehr verwaltete Datenträger zu Knotentypen innerhalb eines Clusters hinzufügen. Sie können den Laufwerksbuchstaben, den Datenträgertyp und die Größe pro Datenträger angeben.

Konfigurieren Sie weitere verwaltete Datenträger, indem Sie die Eigenschaft `additionalDataDisks` und die erforderlichen Parameter in Ihrer Resource Manager-Vorlage wie folgt deklarieren:

**Funktionsanforderungen**
* Lun muss pro Datenträger eindeutig sein und kann nicht das reservierte Lun 0 verwenden
* Der Datenträger kann nicht die reservierten Buchstaben C oder D verwenden und kann nach der Erstellung nicht mehr geändert werden. S wird als Standard verwendet, wenn nicht angegeben.
* Muss einen [unterstützten Datenträger-Typ](how-to-managed-cluster-managed-disk.md) angeben
* Die apiVersion der von Service Fabric verwalteten Clusterressource muss **2021-11-01-preview** oder höher sein.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "additionalDataDisks": {
                    "lun": "1",
                    "diskSizeGB": "50",
                    "diskType": "Standard_LRS",
                    "diskLetter": "S" 
            }
        }
     }
```

Siehe [vollständige Liste der verfügbaren Parameter](/azure/templates/microsoft.servicefabric/2021-11-01/managedclusters)

## <a name="configure-the-service-fabric-data-disk-drive-letter-preview"></a>Konfigurieren Sie den Laufwerksbuchstaben des Datenträgers der Service Fabric (Vorschau)
Service Fabric-verwaltete Cluster konfigurieren standardmäßig einen Service Fabric-Datenträger und konfigurieren automatisch den Laufwerksbuchstaben auf allen Knoten eines Knotentyps. Wenn Sie diese optionale Eigenschaft und diesen Wert konfigurieren, können Sie den Datenträger der Service Fabric angeben und abrufen, wenn Sie spezielle Anforderungen an die Zuordnung von Laufwerksbuchstaben haben.

**Funktionsanforderungen**
* Der Datenträger kann nicht die reservierten Buchstaben C oder D verwenden und kann nach der Erstellung nicht mehr geändert werden. S wird als Standard verwendet, wenn nicht angegeben.
* Die apiVersion der von Service Fabric verwalteten Clusterressource muss **2021-11-01-preview** oder höher sein.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "dataDiskLetter": "S"      
            }
        }
     }
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Automatische Skalierung eines von der Service Fabric verwalteten Clusterknotentyps](how-to-managed-cluster-autoscale.md)
> [!div class="nextstepaction"]
> [Konfigurationsoptionen für verwaltete Service Fabric-Cluster](how-to-managed-cluster-configuration.md)
> [!div class="nextstepaction"]
> [Bereitstellen einer App in einem verwalteten Service Fabric-Cluster](./tutorial-managed-cluster-deploy-app.md)


[overview]: ./media/how-to-managed-cluster-modify-node-type/sfmc-overview.png
[node-type-updating]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-type-updating.png
[adjust-node-count]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-counts.png
[change-nodetype-os-image]: ./media/how-to-managed-cluster-modify-node-type/sfmc-change-os-image.png
[nodetype-placement-property]: ./media/how-to-managed-cluster-modify-node-type/sfmc-nodetype-placement-property.png
[addremove]: ./media/how-to-managed-cluster-modify-node-type/sfmc-addremove-node-type.png

