---
title: Konfigurieren der automatischen Skalierung für von Service Fabric verwaltete Clusterknoten
description: Hier erfahren Sie, wie Sie Richtlinien für die automatische Skalierung für von Service Fabric verwaltete Cluster konfigurieren.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 6c2dd14535b726d242d7076bcac94894e4c560dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095512"
---
# <a name="introduction-to-autoscaling-on-service-fabric-managed-clusters-preview"></a>Einführung in die automatische Skalierung für von Service Fabric verwaltete Cluster (Vorschau)
Die [automatische Skalierung](../azure-monitor/autoscale/autoscale-overview.md) bietet einen hohen Grad an Flexibilität und ermöglicht ein bedarfsgerechtes Erhöhen oder Reduzieren der Knotenanzahl für sekundäre Knotentypen. Dieses automatisierte und flexible Verhalten verringert den Verwaltungsaufwand und die potenziellen geschäftlichen Auswirkungen, indem die Knotenanzahl für Ihre Workload überwacht und optimiert wird. Sie konfigurieren Regeln für Ihre Workload, und die automatische Skalierung kümmert sich um den Rest. Wenn die definierten Schwellenwerte erreicht werden, greifen die Regeln für die automatische Skalierung, und die Kapazität Ihres Knotentyps wird angepasst. Die automatische Skalierung kann jederzeit aktiviert, deaktiviert oder konfiguriert werden. In diesem Artikel wird beispielhaft das Bereitstellen, Aktivieren und Deaktivieren der automatischen Skalierung sowie das Konfigurieren einer Richtlinie für die automatische Skalierung erläutert.


**Anforderungen und unterstützte Metriken:**
* Damit Sie die automatische Skalierung für verwaltete Cluster nutzen können, müssen Sie Version `2021-07-01-preview` oder höher der API verwenden.
* Die Cluster-SKU muss eine Standard-SKU sein.
* Die automatische Skalierung kann nur für sekundäre Knotentypen in Ihrem Cluster konfiguriert werden.
* Legen Sie nach dem Aktivieren der Autoskalierung für einen Knotentyp bei der erneuten Bereitstellung der Ressource die Eigenschaft `vmInstanceCount` auf `-1` fest.
* Nur die [für Azure Monitor veröffentlichten Metriken](../azure-monitor/essentials/metrics-supported.md) werden unterstützt.

Ein häufig auftretendes Szenario, in dem die automatische Skalierung hilfreich ist, ist, wenn die Last eines bestimmten Diensts im Laufe der Zeit schwankt. Beispielsweise kann ein Dienst wie ein Gateway basierend auf dem Umfang an Ressourcen skaliert werden, der zum Verarbeiten eingehender Anforderungen erforderlich ist. Hier ein Beispiel dafür, wie diese Skalierungsregeln aussehen könnten, die im weiteren Verlauf dieses Artikels verwendet werden:
* Wenn für alle Instanzen meines Gateways die Auslastung bei durchschnittlich mehr als 70 % liegt, soll der Gatewaydienst durch Hinzufügen zweier weiterer Instanzen aufskaliert werden. Dieser Vorgang soll alle 30 Minuten ausgeführt werden. Es sollen jedoch nie mehr als insgesamt 20 Instanzen vorhanden sein.
* Wenn alle Instanzen meines Gateways im Durchschnitt weniger als 40 % der Kerne verwenden, soll der Dienst durch Entfernen einer Instanz abskaliert werden. Dieser Vorgang soll alle 30 Minuten ausgeführt werden. Es sollen jedoch nie weniger als insgesamt 3 Instanzen vorhanden sein.

## <a name="example-autoscale-deployment"></a>Beispielbereitstellung mit automatischer Skalierung

In diesem Beispiel wird Folgendes beschrieben: 
* Erstellen eines in Service Fabric verwalteten Clusters mit Standard-SKU und den beiden Standardknotentypen `NT1` und `NT2`
* Hinzufügen von Regeln für die automatische Skalierung zum sekundären Knotentyp `NT2`

>[!NOTE] 
> Die automatische Skalierung für den Knotentyp erfolgt basierend auf den VMSS-CPU-Hostmetriken für den verwalteten Cluster. Die VMSS-Ressource wird in der Vorlage automatisch aufgelöst. 


Im Folgenden wird ausführlich erläutert, wie Sie einen Cluster mit konfigurierter automatischer Skalierung einrichten.

1) Erstellen Sie eine Ressourcengruppe in einer Region.

   ```powershell 
   Login-AzAccount
   Select-AzSubscription -SubscriptionId $subscriptionid
   New-AzResourceGroup -Name $myresourcegroup -Location $location
   ```

2) Erstellen Sie eine Clusterressource.

   Laden Sie das verlinkte [Beispiel für einen in Service Fabric verwalteten Cluster mit Standard-SKU](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/azuredeploy.json) herunter. Führen Sie den folgenden Befehl aus, um die Clusterressource bereitzustellen:

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   adminPassword = $VmAdminPassword
   clientCertificateThumbprint = $clientCertificateThumbprint
   } 
   New-AzResourceGroupDeployment -Name "deploy_cluster" -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterObject $parameters -Verbose
   ```

3) Konfigurieren und Aktivieren Sie Regeln für die automatische Skalierung für einen sekundären Knotentyp.
 
   Laden Sie die [Beispielvorlage für einen verwalteten Cluster mit automatischer Skalierung](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) herunter. Diese Vorlage verwenden Sie dann, um mit den folgenden Befehlen die automatische Skalierung zu konfigurieren:

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   }
   New-AzResourceGroupDeployment -Name "deploy_autoscale" -ResourceGroupName $resourceGroupName -TemplateFile .\sfmc-deploy-autoscale.json -TemplateParameterObject $parameters -Verbose 
   ```

>[!NOTE]
> Nach Abschluss dieser Bereitstellung sollte bei künftigen Clusterressourcenbereitstellungen für sekundäre Knotentypen, für die Regeln für die automatische Skalierung aktiviert sind, die Eigenschaft `vmInstanceCount` auf `-1` festgelegt werden. Dadurch wird sichergestellt, dass es zu keinem Konflikt zwischen Clusterbereitstellungen und der automatischen Skalierung kommt.


## <a name="enable-or-disable-autoscaling-on-a-secondary-node-type"></a>Aktivieren oder Deaktivieren der automatischen Skalierung für einen sekundären Knotentyp

Für Knotentypen, die von einem von Service Fabric verwalteten Cluster bereitgestellt werden, ist die automatische Skalierung standardmäßig nicht aktiviert. Sie kann jederzeit für die einzelnen Knotentypen aktiviert oder deaktiviert werden, die konfiguriert und verfügbar sind.

Konfigurieren Sie in einer ARM-Vorlage unter dem Typ `Microsoft.Insights/autoscaleSettings` die Eigenschaft `enabled` wie unten zu sehen, um dieses Feature zu aktivieren:

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": true,
            ...
```

Wenn Sie die automatische Skalierung deaktivieren möchten, legen Sie den Wert auf `false` fest.

## <a name="delete-autoscaling-rules"></a>Löschen von Regeln für die automatische Skalierung

Wenn Sie für einen Knotentyp eingerichtete Richtlinien für die automatische Skalierung löschen möchten, können Sie den folgenden PowerShell-Befehl ausführen.

```PowerShell
Remove-AzResource -ResourceId "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/microsoft.insights/autoscalesettings/$name" -Force
```

## <a name="set-policies-for-autoscaling"></a>Festlegen von Richtlinien für die automatische Skalierung

 Für von Service Fabric verwaltete Cluster werden standardmäßig keine [Richtlinien für die automatische Skalierung](../azure-monitor/autoscale/autoscale-understanding-settings.md) konfiguriert. Richtlinien für die automatische Skalierung müssen für alle Skalierungsaktionen konfiguriert werden, die für die zugrunde liegenden Ressourcen ausgeführt werden sollen.

Im folgenden Beispiel wird in einer Richtlinie festgelegt, dass für `nodeType2Name` mindestens 3 Knoten vorhanden sein sollen und eine Skalierung auf bis zu 20 Knoten möglich ist. Diese Richtlinie löst ein Hochskalieren aus, wenn die durchschnittliche CPU-Auslastung in den letzten 30 Minuten bei einer Granularität von einer Minute bei 70 % lag. Sie löst ein Herunterskalieren aus, wenn die durchschnittliche CPU-Auslastung in den letzten 30 Minuten bei einer Granularität von einer Minute bei weniger als 40 % lag.

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": "[parameters('enableAutoScale')]",
                "profiles": [
                    {
                        "name": "Autoscale by percentage based on CPU usage",
                        "capacity": {
                            "minimum": "3",
                            "maximum": "20",
                            "default": "3"
                        },
                        "rules": [
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "GreaterThan",
                                  "threshold": 70
                                },
                                "scaleAction": {
                                  "direction": "Increase",
                                  "type": "ChangeCount",
                                  "value": "5",
                                  "cooldown": "PT5M"
                                }
                            },
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "LessThan",
                                  "threshold": 40
                                },
                                "scaleAction": {
                                  "direction": "Decrease",
                                  "type": "ChangeCount",
                                  "value": "1",
                                  "cooldown": "PT5M"
                                }
                            }
                            ]
                    }
                    ]
                }
            }
        
    ]                           
```

Die [ARM-Vorlage für das Aktivieren der automatischen Skalierung](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) mit dem obigen Beispiel können Sie auf der verlinkten Seite herunterladen.


## <a name="view-configured-autoscale-definitions-of-your-managed-cluster-resource"></a>Anzeigen konfigurierter Definitionen für die automatische Skalierung für Ihre verwaltete Clusterressource

Die für die automatische Skalierung konfigurierten Einstellungen können Sie über den [Azure-Ressourcen-Explorer](https://resources.azure.com/) anzeigen.

1) Wechseln Sie zum [Azure-Ressourcen-Explorer](https://resources.azure.com/).

2) Navigieren Sie zu `subscriptions` -> `SubscriptionName` -> `resource group` -> `microsoft.insights` -> `autoscalesettings` -> Name der Richtlinie für die automatische Skalierung, z. B. `sfmc01-NT2`. 

   Die angezeigte Navigationsstruktur entspricht in etwa der folgenden:

   ![Beispielstrukturansicht im Azure-Ressourcen-Explorer][autoscale-are-tree]


3) Auf der rechten Seite wird die vollständige Definition dieser Einstellung für die automatische Skalierung angezeigt. 

   In diesem Beispiel wird die automatische Skalierung mit einer auf der CPU-Auslastung in Prozent basierenden Auf- und Abskalierungsregel konfiguriert.

   ![Beispieldetails für die automatische Skalierung für einen Knotentyp im Azure-Ressourcen-Explorer][autoscale-nt-details]



## <a name="troubleshooting"></a>Problembehandlung

Einige Aspekte, die zu berücksichtigen sind: 

* Gleichen Sie die ausgelösten Ereignisse für die automatische Skalierung mit den sekundären Knotentypen der verwalteten Cluster ab.

   1) Navigieren Sie zum Aktivitätsprotokoll für den Cluster.
   2) Überprüfen Sie das Aktivitätsprotokoll auf abgeschlossene automatische Hoch- und Herunterskalierungsvorgänge.

* Wie viele VMs sind für den Knotentyp konfiguriert, und wird die Workload auf allen oder nur einigen VMs ausgeführt?

* Liegen die Schwellenwerte für das horizontale Herunterskalieren und das horizontale Hochskalieren weit genug auseinander?

   Angenommen, Sie legen eine Regel mit folgenden Bedingungen fest: Wenn die durchschnittliche CPU-Auslastung mindestens fünf Minuten lang bei über 50 Prozent liegt, wird aufskaliert, bei einer durchschnittlichen CPU-Auslastung unter 50 Prozent wird abskaliert. Diese Einstellung würde zu einem Fluktuationsproblem führen, wenn sich die CPU-Nutzung in der Nähe dieses Schwellenwerts bewegt, und die Gruppe würde durch die Skalierungsaktionen fortwährend vergrößert und verkleinert. Aus diesem Grund versucht der Dienst für die automatische Skalierung, die Fluktuation zu verhindern, was dazu führen kann, dass keine Skalierung stattfindet. Achten Sie daher darauf, dass die Schwellenwerte für das horizontale Hochskalieren und das horizontale Herunterskalieren weit genug auseinander liegen, um einen gewissen Skalierungsspielraum zu erhalten.

* Können Sie einen Knotentyp ab- oder aufskalieren?
   Passen Sie die Anzahl der Knoten auf Knotentypebene an, und stellen Sie sicher, dass dieser Vorgang erfolgreich abgeschlossen wird. [Skalieren eines Knotentyps in einem verwalteten Cluster](./how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-a-template)

* Überprüfen Sie die Ressourcen unter Microsoft.ServiceFabric/managedclusters/nodetypes und Microsoft.Insights im Azure-Ressourcen-Explorer.

   Der Azure-Ressourcen-Explorer ist ein unverzichtbares Tool für die Problembehandlung, das Aufschluss über den Zustand Ihrer Azure Resource Manager-Ressourcen gibt. Klicken Sie auf Ihr Abonnement, und sehen Sie sich die Ressourcengruppe an, für die Sie die Problembehandlung ausführen. Suchen Sie bei dem Ressourcenanbieter `ServiceFabric/managedclusters/clustername` unter `NodeTypes` nach den von Ihnen erstellten Knotentypen, und überprüfen Sie die Eigenschaften, um sicherzustellen, dass der Wert für `provisioningState` `Succeeded` ist. Navigieren Sie anschließend zum Ressourcenanbieter Microsoft.Insights, und überprüfen Sie unter `clustername`, ob die Regeln für die automatische Skalierung richtig aussehen. 

* Entsprechen die ausgegebenen Metrikwerte den Erwartungen?
   Rufen Sie mit dem [PowerShell-Modul](/powershell/module/az.monitor/get-azmetric) `Get-AzMetric` die Metrikwerte einer Ressource ab, und überprüfen Sie sie.


Wenn Sie diese Schritte durchgeführt haben und weiterhin Probleme mit der automatischen Skalierung vorliegen, können Sie versuchen, die Probleme mithilfe der folgenden Ressourcen zu lösen: [Erstellen einer Azure-Supportanfrage](./service-fabric-support.md#create-an-azure-support-request). Bereiten Sie das Teilen der Vorlage und einer Ansicht Ihrer Leistungsdaten vor.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Informationen zum Support für die automatische Skalierung in Azure Monitor](../azure-monitor/autoscale/autoscale-overview.md)
> [!div class="nextstepaction"]
> [Überprüfen von Metriken in Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
> [!div class="nextstepaction"]
> [Konfigurationsoptionen für verwaltete Service Fabric-Cluster](how-to-managed-cluster-configuration.md)

[autoscale-are-tree]: ./media/how-to-managed-cluster-autoscale/autoscale-are-tree.png
[autoscale-nt-details]: ./media/how-to-managed-cluster-autoscale/autoscale-nt-details.png


