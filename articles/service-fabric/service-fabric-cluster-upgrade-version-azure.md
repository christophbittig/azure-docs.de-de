---
title: Verwalten von Service Fabric-Clusterupgrades
description: Verwalten Sie Ort und Art des Updates Ihrer Service Fabric-Clusterruntime.
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 129bdae4dc131013bd7c13377b61575141c27ccd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714570"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Verwalten von Service Fabric-Clusterupgrades

Ein Azure Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. Hier erfahren Sie, wie Sie den Zeitpunkt und die Art der Aktualisierung Ihres Azure Service Fabric-Clusters durch Microsoft verwalten.

Weitere Hintergrundinformationen zu Konzepten und Prozessen für Clusterupgrades finden Sie unter [Upgrade und Update von Azure Service Fabric-Clustern](service-fabric-cluster-upgrade.md).

## <a name="set-upgrade-mode"></a>Festlegen des Upgrademodus

Sie können für Ihren Cluster festlegen, dass er automatische Service Fabric-Upgrades erhält, sobald sie von Microsoft veröffentlicht werden, oder Sie können manuell aus einer Liste der derzeit unterstützten Versionen auswählen, indem Sie den Upgrademodus für Ihren Cluster festlegen. Dies kann entweder über das Steuerelement *Fabric-Upgrademodus* im Azure-Portal oder die `upgradeMode`-Einstellung in der Vorlage für die Clusterbereitstellung erfolgen.

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie das Azure-Portal verwenden, wählen Sie beim Erstellen eines neuen Service Fabric-Clusters zwischen automatischen und manuellen Upgrades.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Auswahl zwischen automatischen und manuellen Upgrades beim Erstellen eines neuen Clusters im Azure-Portal in den „Erweitert“-Optionen":::

Sie können auch im Abschnitt **Fabric-Upgrades** einer vorhandenen Clusterressource zwischen automatischen und manuellen Upgrades umschalten.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Auswählen von automatischen oder manuellen Upgrades im Abschnitt „Fabric-Upgrades“ Ihrer Clusterressource im Azure-Portal":::

### <a name="manual-upgrades-with-azure-portal"></a>Manuelle Upgrades im Azure-Portal

Wenn Sie die manuelle Upgradeoption auswählen, müssen Sie zum Initiieren eines Upgrades lediglich die verfügbaren Versionen in der Dropdownliste und dann *Speichern* auswählen. Von dort aus wird das Clusterupgrade sofort gestartet.

Bei dem Upgrade werden die [Clusterintegritätsrichtlinien](#custom-policies-for-manual-upgrades) (eine Kombination aus Knotenintegrität und Integrität aller im Cluster ausgeführten Anwendungen) berücksichtigt. Wenn die Clusterintegritätsrichtlinien nicht erfüllt sind, wird das Upgrade zurückgesetzt.

Beheben Sie die Probleme, die zu dem Rollback geführt haben, und initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte ausführen wie zuvor.

### <a name="resource-manager-template"></a>Resource Manager-Vorlage

Wenn Sie den Clusterupgrademodus mithilfe einer Resource Manager-Vorlage ändern möchten, geben Sie für die `upgradeMode`-Eigenschaft der Ressourcendefinition *Microsoft.ServiceFabric/clusters* entweder *Automatisch* oder *Manuell* an. Wenn Sie manuelle Upgrades auswählen, legen Sie auch `clusterCodeVersion` auf eine zurzeit [unterstützte Fabric-Version](#check-for-supported-cluster-versions) fest.

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Screenshot mit Vorlage, in der der Klartext eingerückt ist, um die Struktur widerzuspiegeln. Die Eigenschaften „clusterCodeVersion“ und „upgradeMode“ sind hervorgehoben.":::

Nach erfolgreicher Bereitstellung der Vorlage werden Änderungen am Clusterupgrademodus angewendet. Wenn sich der Cluster im manuellen Modus befindet, wird das Clusterupgrade automatisch gestartet.

Bei dem Upgrade werden die [Clusterintegritätsrichtlinien](#custom-policies-for-manual-upgrades) (eine Kombination aus Knotenintegrität und Integrität aller im Cluster ausgeführten Anwendungen) berücksichtigt. Wenn die Clusterintegritätsrichtlinien nicht erfüllt sind, wird das Upgrade zurückgesetzt.

Beheben Sie die Probleme, die zu dem Rollback geführt haben, und initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte ausführen wie zuvor.

## <a name="wave-deployment-for-automatic-upgrades"></a>Bereitstellung in Zyklen für automatische Upgrades

Mit dem automatischen Upgrademodus haben Sie die Möglichkeit, Ihren Cluster für die Bereitstellung in Zyklen zu aktivieren. Mit der Bereitstellung in Zyklen können Sie eine Pipeline erstellen, mit der Sie Ihre Test-, Bereitstellungs- und Produktionscluster nacheinander, durch die integrierte „Bake Time“ getrennt aktualisieren können, um anstehende Service Fabric-Versionen zu validieren, bevor die Produktionscluster aktualisiert werden.

### <a name="enable-wave-deployment"></a>Aktivieren der Bereitstellung in Zyklen

> [!NOTE]
> Die Bereitstellung in Zyklen erfordert die API-Version `2020-12-01-preview` (oder höher) für Ihre *Microsoft.ServiceFabric/clusters*-Ressource.

Zum Aktivieren der Bereitstellung in Zyklen für das automatische Upgrade legen Sie zunächst fest, welcher Zyklus Ihrem Cluster zugewiesen werden soll:

* **Wave 0** (`Wave0`): Cluster werden aktualisiert, sobald ein neuer Service Fabric-Build freigegeben wird. Für Test/Dev-Cluster vorgesehen.
* **Wave 1** (`Wave1`): Cluster werden eine Woche (sieben Tage), nachdem ein neuer Build freigegeben wurde, aktualisiert. Für Vorproduktions-/Stagingcluster vorgesehen.
* **Wave 2** (`Wave2`): Cluster werden zwei Wochen (14 Tage), nachdem ein neuer Build freigegeben wurde, aktualisiert. Für Produktionscluster vorgesehen.

Fügen Sie dann Ihrer Clusterressourcenvorlage einfach mit einem der oben aufgeführten Wave-Werte eine `upgradeWave`-Eigenschaft hinzu. Stellen Sie sicher, dass Sie über Version `2020-12-01-preview` oder höher der Clusterressourcen-API verfügen.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Nachdem Sie die aktualisierte Vorlage bereitgestellt haben, wird Ihr Cluster im angegebenen Zyklus für den nächsten Upgradezeitraum und danach registriert.

Sie können sich für [E-Mail-Benachrichtigungen registrieren](#register-for-notifications), die Links zu weiterer Hilfe enthalten, wenn bei einem Clusterupgrade ein Fehler auftritt.

### <a name="register-for-notifications"></a>Registrieren für Benachrichtigungen

Sie können sich dafür registrieren, Benachrichtigungen zu erhalten, wenn bei einem Clusterupgrade ein Fehler auftritt. Eine E-Mail mit weiteren Informationen zum Upgradefehler und Links zu weiteren Hilfethemen wird an die angegebene(n) E-Mail-Adresse(n) gesendet.

> [!NOTE]
> Die Registrierung für die Bereitstellung in Zyklen ist nicht erforderlich, um Benachrichtigungen über Upgradefehler zu erhalten.

Um sich für Benachrichtigungen zu registrieren, fügen Sie der Clusterressourcenvorlage einen `notifications`-Abschnitt hinzu, und geben Sie eine oder mehrere E-Mail-Adressen (*receivers*) an, um Benachrichtigungen zu erhalten:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Nachdem Sie die aktualisierte Vorlage bereitgestellt haben, werden Sie für Benachrichtigungen über Upgradefehler registriert.

## <a name="custom-policies-for-manual-upgrades"></a>Benutzerdefinierte Richtlinien für manuelle Upgrades

Sie können benutzerdefinierte Integritätsrichtlinien für manuelle Clusterupgrades angeben. Diese Richtlinien werden jedes Mal angewendet, wenn Sie eine neue Runtimeversion auswählen, wodurch im System der Start des Upgrade Ihres Clusters ausgelöst wird. Wenn Sie die Richtlinien nicht überschreiben, werden die Standardwerte verwendet.

Sie können die benutzerdefinierten Integritätsrichtlinien angeben oder die aktuellen Einstellungen im Abschnitt **Fabric-Upgrades** Ihrer Clusterressource im Azure-Portal überprüfen, indem Sie die Option *Benutzerdefiniert* für **Upgraderichtlinie** auswählen.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Wählen Sie die Upgraderichtlinienoption „Benutzerdefiniert“ im Abschnitt „Fabric-Upgrades“ Ihrer Clusterressource im Azure-Portal aus, um während des Upgrades benutzerdefinierte Integritätsrichtlinien festzulegen.":::

## <a name="check-for-supported-cluster-versions"></a>Überprüfen der unterstützten Clusterversionen

Sie können auf [Service Fabric-Versionen](service-fabric-versions.md) verweisen, um weitere Informationen zu unterstützten Versionen und Betriebssystemen zu erhalten.

Mit der [Azure-REST-API](/rest/api/azure/) können Sie alle verfügbaren Service Fabric-Runtimeversionen ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) auflisten, die für den angegebenen Speicherort und Ihr Abonnement verfügbar sind.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

`supportExpiryUtc` in der Ausgabe meldet, wenn ein bestimmtes Release abläuft oder abgelaufen ist. Die neueste Version besitzt anstelle eines gültigen Datums den Wert *9999-12-31T23:59:59.9999999*, was bedeutet, dass noch kein Ablaufdatum feststeht.


## <a name="check-for-supported-upgrade-path"></a>Überprüfen des unterstützten Upgradepfads

In der Dokumentation zu den [Service Fabric-Versionen](service-fabric-versions.md) finden Sie Informationen zu unterstützten Upgrade-Pfaden und zugehörigen Versionen. 

Mithilfe der Informationen zu unterstützten Zielversionen können Sie die folgenden PowerShell-Schritte verwenden, um den unterstützten Upgradepfad zu überprüfen.

1) Anmelden an Azure
   ```PowerShell
   Login-AzAccount
   ```

2) Auswählen des Abonnements
   ```PowerShell
   Set-AzContext -SubscriptionId <your-subscription>
   ```

3) Aufrufen der API
   ```PowerShell
   $params = @{ "TargetVersion" = "<target version>"}
   Invoke-AzResourceAction -ResourceId -ResourceId <cluster resource id> -Parameters $params -Action listUpgradableVersions -Force
   ```

   Beispiel: 
   ```PowerShell
   $params = @{ "TargetVersion" = "8.1.335.9590"}
   Invoke-AzResourceAction -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabric/clusters/myCluster -Parameters $params -Action listUpgradableVersions -Force

   Output
   supportedPath
   -------------
   {8.1.329.9590, 8.1.335.9590}
   ```


## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Service Fabric-Upgrades](service-fabric-cluster-upgrade-version-azure.md)
* Anpassen Ihrer [Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md)
* [Ab- und Aufskalieren Ihres Clusters](service-fabric-cluster-scale-in-out.md)
* Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
