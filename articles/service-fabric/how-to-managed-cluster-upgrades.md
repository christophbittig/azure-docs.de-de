---
title: Upgrade von verwalteten Azure Service Fabric-Clustern
description: Erfahren Sie mehr über die Optionen zum Aktualisieren Ihres verwalteten Azure Service Fabric-Clusters.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: e03a4cba43fba0fe8e7b1ececc5872b20961f9a8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075066"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>Verwalten von Service Fabric-Clusterupgrades

Ein Azure Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. Erfahren Sie, wie Sie den Zeitpunkt und die Art der Aktualisierung Ihrer Azure Service Fabric-Cluster-Runtime durch Microsoft verwalten.

## <a name="set-upgrade-mode"></a>Festlegen des Upgrademodus

Verwaltete Azure Service Fabric-Cluster werden standardmäßig so festgelegt, dass sie automatische Service Fabric-Upgrades erhalten, wenn sie von Microsoft mithilfe einer [Wave-Bereitstellungsstrategie](#wave-deployment-for-automatic-upgrades) veröffentlicht werden. Alternativ können Sie Upgrades im manuellen Modus einrichten, indem Sie diese aus einer Liste der derzeit unterstützten Versionen auswählen. Dies kann entweder im Azure-Portal über das Steuerelement *Fabric-Upgrades* oder die `ClusterUpgradeMode`-Einstellung in der Vorlage für die Clusterbereitstellung erfolgen.

## <a name="wave-deployment-for-automatic-upgrades"></a>Bereitstellung in Zyklen für automatische Upgrades

Mit der Bereitstellung in Zyklen können Sie eine Pipeline erstellen, mit der Sie Ihre Test-, Bereitstellungs- und Produktionscluster nacheinander, durch die integrierte „Bake Time“ getrennt aktualisieren können, um anstehende Service Fabric-Versionen zu validieren, bevor die Produktionscluster aktualisiert werden.

>[!NOTE]
>Standardmäßig werden Cluster auf Wave 0 festgelegt.

Zum Auswählen der zyklischen Bereitstellung für das automatische Upgrade legen Sie zunächst fest, welcher Zyklus Ihrem Cluster zugewiesen werden soll:

* **Wave 0** (`Wave0`): Cluster werden aktualisiert, sobald ein neuer Service Fabric-Build freigegeben wird.
* **Wave 1** (`Wave1`): Cluster werden nach Wave 0 aktualisiert, um Baking-Zeit zu ermöglichen. Wave 1 erfolgt mindestens 7 Tage nach Wave 0.
* **Wave 2** (`Wave2`): Cluster werden zuletzt aktualisiert, um weitere Baking-Zeit zu ermöglichen. Wave 2 erfolgt mindestens 14 Tage nach Wave 0.

## <a name="set-the-wave-for-your-cluster"></a>Festlegen des Zyklus für Ihren Cluster

Sie können Ihren Cluster im Azure-Portal entweder über das Steuerelement *Fabric-Upgrades* oder die `ClusterUpgradeMode` Einstellung in Ihrer Clusterbereitstellungsvorlage auf eine der verfügbaren Wellen festlegen.

### <a name="azure-portal"></a>Azure-Portal

Im Azure-Portal wählen Sie beim Erstellen eines neuen Service Fabric-Clusters zwischen den verfügbaren automatischen Zyklen aus.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png" alt-text="Wählen Sie beim Erstellen eines neuen Clusters im Azure-Portal unter Optionen „Erweitert“ zwischen verschiedenen verfügbaren Zyklen":::.

Sie können auch im Abschnitt **Fabric-Upgrades** einer vorhandenen Clusterressource zwischen automatischen und manuellen Upgrades umschalten.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png" alt-text="Wählen Sie im Azure-Portal im Abschnitt „Fabric-Upgrades“ Ihrer Clusterressource zwischen verschiedenen automatischen Zyklen":::.

### <a name="resource-manager-template"></a>Resource Manager-Vorlage

Wenn Sie den Clusterupgrademodus mithilfe einer Resource Manager-Vorlage ändern möchten, geben Sie für die `ClusterUpgradeMode`-Eigenschaft der Ressourcendefinition *Microsoft.ServiceFabric/clusters* entweder *Automatisch* oder *Manuell* an. Wenn Sie manuelle Upgrades auswählen, legen Sie auch `clusterCodeVersion` auf eine zurzeit [unterstützte Fabric-Version](#query-for-supported-cluster-versions) fest.

#### <a name="manual-upgrade"></a>Manuelles Upgrade

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedClusters",
  "properties": {
    "clusterUpgradeMode": "Manual",
    "clusterCodeVersion": "8.0.514.9590"
  }
}
```

Nach erfolgreicher Bereitstellung der Vorlage werden Änderungen am Clusterupgrademodus angewendet. Wenn sich der Cluster im manuellen Modus befindet, wird das Clusterupgrade automatisch gestartet.

Während des Upgradevorgangs werden die Clusterintegritätsrichtlinien berücksichtigt (eine Kombination aus Knotenintegrität und Integrität aller im Cluster ausgeführten Anwendungen). Wenn die Clusterintegritätsrichtlinien nicht erfüllt sind, wird das Upgrade zurückgesetzt.

Beim Auftreten eines Rollbacks müssen Sie die Probleme beheben, die zum Rollback geführt haben, und das Upgrade erneut initiieren, indem Sie die gleichen Schritte wie zuvor ausführen.

#### <a name="automatic-upgrade-with-wave-deployment"></a>Automatisches Upgrade mit Wave-Bereitstellung

Um automatische Upgrades und die Wave-Bereitstellung zu konfigurieren, wird einfach „`ClusterUpgradeMode` Hinzufügen/Validieren“ auf `Automatic` festgelegt und die `clusterUpgradeCadence`-Eigenschaft wird mit einem der Wave-Werte definiert, die oben in Ihrer Resource Manager-Vorlage aufgeführt sind.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedClusters",
  "properties": {
    "clusterUpgradeMode": "Automatic",
    "clusterUpgradeCadence": "Wave1"
  }
}
```

Nachdem Sie die aktualisierte Vorlage bereitgestellt haben, wird Ihr Cluster im angegebenen Zyklus für automatische Upgrades registriert.

## <a name="query-for-supported-cluster-versions"></a>Abfrage nach unterstützten Clusterversionen

Mit der [Azure-REST-API](/rest/api/azure/) können Sie alle verfügbaren Service Fabric-Runtimeversionen ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) auflisten, die für den angegebenen Speicherort und Ihr Abonnement verfügbar sind.

Sie können auch auf [Service Fabric-Versionen](service-fabric-versions.md) verweisen, um weitere Informationen zu unterstützten Versionen und Betriebssystemen zu erhalten.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/managedclusterVersions?api-version=2021-05-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/7.2.477.9590",
    "name": "7.2.477.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "2021-11-30T00:00:00",
      "osType": "Windows",
      "clusterCodeVersion": "7.2.477.9590"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/8.0.514.9590",
    "name": "8.0.514.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "osType": "Windows",
      "clusterCodeVersion": "8.0.514.9590"
    }
  }
]

```

`supportExpiryUtc` in der Ausgabe meldet, wenn ein bestimmtes Release abläuft oder abgelaufen ist. Die neueste Version besitzt anstelle eines gültigen Datums den Wert *9999-12-31T23:59:59.9999999*, was bedeutet, dass noch kein Ablaufdatum feststeht.

## <a name="next-steps"></a>Nächste Schritte

* [Anpassen Ihrer Konfiguration für verwaltete Service Fabric-Cluster](how-to-managed-cluster-configuration.md)
* Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[Upgrade-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png
[New-Cluster-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png
