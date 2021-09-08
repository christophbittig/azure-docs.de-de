---
title: Upgrade der intelligenten Erkennung von Azure Monitor Application Insights auf Warnungen (Vorschau) | Microsoft-Dokumentation
description: Erfahren Sie mehr über die erforderlichen Schritte zum Upgrade der intelligenten Erkennung von Azure Monitor Application Insights auf Warnungsregeln.
ms.topic: conceptual
ms.date: 05/30/2021
ms.openlocfilehash: d0995c05af707c5a4fb94305ca1211e38ca59e3a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587479"
---
# <a name="migrate-azure-monitor-application-insights-smart-detection-to-alerts-preview"></a>Migrieren der intelligenten Erkennung von Azure Monitor Application Insights zu Warnungen (Vorschau)

In diesem Artikel wird beschrieben, wie Sie die intelligente Erkennung von Application Insights zu Warnungen migrieren. Bei der Migration werden Warnungsregeln für die verschiedenen Module der intelligenten Erkennung erstellt. Sie können diese Regeln wie beliebige andere Azure Monitor-Warnungsregeln verwalten und konfigurieren. Darüber hinaus können Sie auch Aktionsgruppen für diese Regeln konfigurieren, wodurch verschiedene Methoden für Aktionen oder Benachrichtigungen für neue Erkennungen bereitgestellt werden.

## <a name="benefits-of-migration-to-alerts"></a>Vorteile der Migration zu Warnungen

Nach der Migration können Sie über die intelligente Erkennung alle Funktionen der Azure Monitor-Warnungen nutzen, darunter:

- **Umfassende Benachrichtigungsoptionen für alle Erkennungen** -  Mithilfe von [Aktionsgruppen](../alerts/action-groups.md) können Sie verschiedene Arten von Benachrichtigungen und Aktionen konfigurieren, die bei Auftreten einer Warnung ausgelöst werden. Sie können Benachrichtigungen per E-Mail, SMS, Sprachanruf oder Pushbenachrichtigung konfigurieren und beispielsweise Aktionen zum Aufrufen eines sicheren Webhooks, einer Logik-App, eines Automation-Runbooks und mehr konfigurieren. Aktionsgruppen unterstützen die bedarfsorientierte Verwaltung, indem sie es Ihnen ermöglichen, Aktionen einmalig zu konfigurieren und anschließend für mehrere Warnungsregeln zu verwenden.
- **Bedarfsorientierte Verwaltung** von Warnungen der intelligenten Erkennung mithilfe der Azure Monitor-Warnungsfunktionalität und -API.
- **Regelbasierte Unterdrückung von Benachrichtigungen** -  Mithilfe von [Aktionsregeln](../alerts/alerts-action-rules.md) können Sie Aktionen für einen beliebigen Resource Manager-Bereich (Abonnement, Ressourcengruppe oder Zielressource) definieren oder unterdrücken. Sie verfügen über verschiedene Filter zum Eingrenzen auf eine bestimmte Teilmenge von Warnungsinstanzen, auf die Sie reagieren möchten. 

## <a name="migrated-smart-detection-capabilities"></a>Migrierte Funktionen der intelligenten Erkennung

Beim Migrieren einer Application Insights-Ressource wird ein neuer Satz von Warnungsregeln erstellt.  Für jede migrierte Funktion der intelligenten Erkennung wird eine Regel erstellt. In der folgenden Tabelle werden die Funktionen der intelligenten Erkennung vor der Migration den Warnungsregeln nach der Migration zugeordnet:

| Name der intelligente Erkennungsregel <sup>(1)</sup>          | Name der Warnungsregel <sup>(2)</sup> |
| -------------------------                          | ------------------------------ |
| Beeinträchtigung der Serverantwortzeit                | Beeinträchtigung der Antwortlatenz: *\<Application Insights resource name\>*  |
| Leistungsminderung der Abhängigkeitsdauer                 | Beeinträchtigung der Abhängigkeitslatenz: *\<Application Insights resource name\>*|
| Verschlechterung des Schweregrads der Ablaufverfolgung (Vorschau)      | Verschlechterung des Schweregrads der Ablaufverfolgung: *\<Application Insights resource name\>*|
| Anormaler Anstieg in Ausnahmevolume (Vorschau)        | Ausnahmeanomalien: *\<Application Insights resource name\>*|
| Möglicher Speicherverluste erkannt (Vorschau)           | Potenzieller Arbeitsspeicherverlust: *\<Application Insights resource name\>*|
| Langsame Seitenladezeit                                | *eingestellt* <sup>(3)</sup>  |
| Langsame Serverantwortzeit                          | *eingestellt* <sup>(3)</sup>  |
| Lange Abhängigkeitsdauer                           | *eingestellt* <sup>(3)</sup>  |
| Mögliches Sicherheitsproblem erkannt (Vorschau)        | *eingestellt* <sup>(3)</sup>  |
| Anormaler Anstieg des täglichen Datenvolumens (Vorschauversion)       | *eingestellt* <sup>(3)</sup>  |

<sup>(1)</sup> Name der Regel auf dem Blatt „Einstellungen“ der intelligenten Erkennung  
<sup>(2)</sup> Name der neuen Warnungsregel nach der Migration  
<sup>(3)</sup> Diese Funktionen der intelligenten Erkennung werden aufgrund einer geringen Nutzung und einer Neubewertung der Erkennungseffizienz nicht konvertiert. Nach Abschluss der Migration werden diese Erkennungen für die Ressource nicht länger unterstützt. 

Die Migration zieht keine Änderungen am algorithmischen Entwurf und Verhalten der intelligenten Erkennung nach sich. Vor und nach der Migration wird die gleiche Erkennungsleistung erwartet.

Sie müssen die Migration auf jede Application Insights-Ressource einzeln anwenden. Für Ressourcen, die nicht explizit migriert werden, funktioniert die intelligente Erkennung weiterhin wie zuvor.

### <a name="action-group-configuration-for-the-new-smart-detection-alert-rules"></a>Aktionsgruppenkonfiguration für die neuen Warnungsregeln der intelligenten Erkennung

Im Rahmen der Migration wird jede neue Warnungsregel automatisch mit einer Aktionsgruppe konfiguriert. Die Migration kann für jede Regel eine Standardaktionsgruppe zuweisen. Die Standardaktionsgruppe wird gemäß der Regelbenachrichtigung vor der Migration konfiguriert:

- Wenn für die **intelligente Erkennungsregel die Standard-E-Mail-Benachrichtigung oder keine Benachrichtigung konfiguriert war**, wird die neue Warnungsregel mit einer Aktionsgruppe namens „Application Insights – intelligente Erkennung“ konfiguriert.
    - Wenn das Migrationstool eine vorhandene Aktionsgruppe mit diesem Namen findet, verknüpft es die neue Warnungsregel mit dieser Aktionsgruppe.  
    - Andernfalls wird eine neue Aktionsgruppe mit diesem Namen erstellt. Die neue Gruppe wird für die Aktion „E-Mail an Azure Resource Manager-Rollen“ konfiguriert und sendet Benachrichtigungen an Ihre Benutzer „Mitwirkender an der Überwachung“ und „Benutzer mit Leseberechtigung für Überwachungsdaten“ für Azure Resource Manager.

- Wenn die **Standard-E-Mail-Benachrichtigung vor der Migration geändert wurde**, wird eine Aktionsgruppe namens „Application Insights – intelligente Erkennung \<n\>“ mit einer E-Mail-Aktion erstellt, die Benachrichtigungen an die zuvor konfigurierten E-Mail-Adressen sendet.

Anstatt die Standardaktionsgruppe zu verwenden, wählen Sie eine vorhandene Aktionsgruppe aus, die für alle neuen Warnungsregeln konfiguriert wird.

## <a name="executing-smart-detection-migration-process"></a>Ausführen des Migrationsprozesses für die intelligente Erkennung

### <a name="migrate-your-smart-detection-using-the-azure-portal"></a>Migrieren der intelligenten Erkennung mithilfe des Azure-Portals

Wenden Sie die Migration jeweils auf eine bestimmte Application Insights-Ressource an.

Führen Sie die folgenden Schritte aus, um die intelligente Erkennung in Ihrer Ressource zu migrieren:

1. Wählen Sie in der Application Insights-Ressource im links angezeigten Menü unter **Untersuchen** die Option **Intelligente Erkennung** aus.

2. Klicken Sie auf das Banner mit dem Titel **Intelligente Erkennung zu Warnungen migrieren (Vorschau)** . Das Dialogfeld für die Migration wird geöffnet.

   ![Banner zum Feed der intelligenten Erkennung](media/alerts-smart-detections-migration/smart-detection-feed-banner.png)

3. Wählen Sie eine Aktionsgruppe aus, die für die neuen Warnungsregeln konfiguriert werden soll. Sie können zwischen der Standardaktionsgruppe (wie oben erläutert) oder einer Ihrer vorhandenen Aktionsgruppen wählen.

4. Klicken Sie auf **Migrieren**, um den Migrationsvorgang zu starten.

   ![Dialogfeld für die Migration der intelligenten Erkennung](media/alerts-smart-detections-migration/smart-detection-migration-dialog.png)

Nach der Migration werden neue Warnungsregeln für Ihre Application Insight-Ressource erstellt, wie oben erläutert.

### <a name="migrate-your-smart-detection-using-azure-cli"></a>Migrieren der intelligenten Erkennung mithilfe der Azure CLI

Sie können die Migration der intelligenten Erkennung mithilfe des folgenden Azure CLI-Befehls starten. Der Befehl löst den vorkonfigurierten Migrationsprozess wie zuvor beschrieben aus.

```azurecli
az rest --method POST --uri /subscriptions/{subscriptionId}/providers/Microsoft.AlertsManagement/migrateFromSmartDetection?api-version=2021-01-01-preview --body @body.txt
```

Dabei muss „body.txt“ Folgendes enthalten:

```json
{
      "scope": [
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} /providers/microsoft.insights/components/{resourceName} "
      ],
      "actionGroupCreationPolicy" : "{Auto/Custom}",
      "customActionGroupName" : "{actionGroupName}"           
}
```

Über **ActionGroupCreationPolicy** wird die Richtlinie zum Migrieren der E-Mail-Einstellungen in den intelligenten Erkennungsregeln in Aktionsgruppen ausgewählt. Zulässige Werte sind:

- **Auto**: Verwendet die Standardaktionsgruppen wie in diesem Dokument beschrieben.
- **Custom**: Erstellt alle Warnungsregeln mit der in **customActionGroupName** angegebenen Aktionsgruppe.
- *\<blank\>* : Wenn **ActionGroupCreationPolicy** nicht angegeben ist, wird die Richtlinie „Auto“ verwendet.

### <a name="migrate-your-smart-detection-using-azure-resource-manager-templates"></a>Migrieren der intelligenten Erkennung mithilfe von Azure Resource Manager-Vorlagen

Sie können die Migration der intelligenten Erkennung zu Warnungen für eine bestimmte Application Insights-Ressource auslösen, indem Sie Azure Resource Manager-Vorlagen verwenden. Bei Verwendung dieser Methode müssen Sie folgende Aufgaben ausführen:

- Erstellen einer Warnungsregel für die intelligente Erkennung für jede unterstützte Erkennung
- Ändern der Application Insight-Eigenschaften, um anzugeben, dass die Migration abgeschlossen wurde

Bei Verwendung dieser Methode können Sie steuern, welche Warnungsregeln erstellt werden sollen, Sie können eigene Warnungsregelnamen und eine Beschreibung definieren und eine beliebige Aktionsgruppe für jede Regel auswählen.

Zu diesem Zweck sollten die folgenden Vorlagen verwendet werden (bearbeiten Sie diese nach Bedarf, um Ihre Abonnement-ID und den Namen Ihrer Application Insights-Ressource anzugeben).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "applicationInsightsResourceName": {
            "type": "string"
        },
        "actionGroupName": {
            "type": "string",
            "defaultValue": "Application Insights Smart Detection"
        },
        "actionGroupResourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().Name]"
        }
    },
    "variables": {
        "applicationInsightsResourceId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().Name,'/providers/microsoft.insights/components/',parameters('applicationInsightsResourceName'))]",
        "actionGroupId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',parameters('actionGroupResourceGroup'),'/providers/microsoft.insights/ActionGroups/',parameters('actionGroupName'))]",
        "requestPerformanceDegradationDetectorRuleName": "[concat('Response Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "dependencyPerformanceDegradationDetectorRuleName": "[concat('Dependency Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "traceSeverityDetectorRuleName": "[concat('Trace Severity Degradation - ', parameters('applicationInsightsResourceName'))]",
        "exceptionVolumeChangedDetectorRuleName": "[concat('Exception Anomalies - ', parameters('applicationInsightsResourceName'))]",
        "memoryLeakRuleName": "[concat('Potential Memory Leak - ', parameters('applicationInsightsResourceName'))]"
    },
    "resources": [
        {
            "name": "[variables('requestPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "RequestPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {   
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('dependencyPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Dependency Latency Degradation notifies you of an unusual increase in response by a dependency your app is calling (e.g. REST API or database)",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "DependencyPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('traceSeverityDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Trace Severity Degradation notifies you of an unusual increase in the severity of the traces generated by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "TraceSeverityDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('exceptionVolumeChangedDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Exception Anomalies notifies you of an unusual rise in the rate of exceptions thrown by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "ExceptionVolumeChangedDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('memoryLeakRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Potential Memory Leak notifies you of increased memory consumption pattern by your app which may indicate a potential memory leak.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "MemoryLeakDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[concat(parameters('applicationInsightsResourceName'),'/migrationToAlertRulesCompleted')]",
            "type": "Microsoft.Insights/components/ProactiveDetectionConfigs",
            "location": "[resourceGroup().location]",
            "apiVersion": "2018-05-01-preview",
            "properties": {
                "name": "migrationToAlertRulesCompleted",
                "sendEmailsToSubscriptionOwners": false,
                "customEmails": [],
                "enabled": true
            },
            "dependsOn": [
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('requestPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('dependencyPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('traceSeverityDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('exceptionVolumeChangedDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('memoryLeakRuleName'))]"
            ]
        }
    ]
}
```

## <a name="viewing-your-alerts-after-the-migration"></a>Anzeigen der Warnungen nach der Migration

Nach dem Migrationsprozess können Sie die Warnungen der intelligenten Erkennung anzeigen, indem Sie in Ihrer Application Insights-Ressource im links angezeigten Menü den Eintrag „Warnungen“ auswählen. Wählen Sie für **Signaltyp** die Option **Intelligente Erkennung** aus, um nur die Warnungen der intelligenten Erkennung zu filtern und anzuzeigen. Sie können eine Warnung auswählen, um deren Erkennungsdetails anzuzeigen.

![Warnungen der intelligenten Erkennung](media/alerts-smart-detections-migration/smart-detector-alerts.png)

Sie können die verfügbaren Erkennungen auch weiterhin im Feed für die intelligente Erkennung Ihrer Application Insights-Ressource anzeigen.

![Feed für intelligente Erkennung](media/alerts-smart-detections-migration/smart-detection-feed.png)

## <a name="managing-smart-detection-alert-rules-settings-after-the-migration"></a>Verwalten der Einstellungen für Warnungsregeln der intelligenten Erkennung nach der Migration

### <a name="managing-alert-rules-settings-using-the-azure-portal"></a>Verwalten von Einstellungen für Warnungsregeln mithilfe des Azure-Portals

Nach Abschluss der Migration greifen Sie auf ähnliche Weise auf die neuen Warnungsregeln der intelligenten Erkennung zu wie auf andere Warnungsregeln, die für die Ressource definiert sind:

1. Wählen Sie in der Application Insights-Ressource im links angezeigten Menü unter **Überwachung** die Option **Warnungen** aus.

   ![Menü „Warnungen“](media/alerts-smart-detections-migration/application-insights-alerts.png)

2. Klicken Sie auf **Warnungsregeln verwalten**.

   ![Verwalten von Warnungsregeln](media/alerts-smart-detections-migration/manage-alert-rules.png)

3. Wählen Sie für **Signaltyp** die Option **Intelligente Erkennung** aus, um die Warnungen der intelligenten Erkennung zu filtern und anzuzeigen.

   ![Regeln für die intelligente Erkennung](media/alerts-smart-detections-migration/smart-detector-rules.png)

### <a name="enabling--disabling-smart-detection-alert-rules"></a>Aktivieren/Deaktivieren von Warnungsregeln der intelligenten Erkennung 

Warnungsregeln der intelligenten Erkennung können wie alle anderen Warnungsregeln über die Benutzeroberfläche des Portals oder programmgesteuert aktiviert oder deaktiviert werden.

Wenn eine bestimmte intelligente Erkennungsregel vor der Migration deaktiviert war, wird die neue Warnungsregel ebenfalls deaktiviert.

### <a name="configuring-action-group-for-your-alert-rules"></a>Konfigurieren einer Aktionsgruppe für Ihre Warnungsregeln

Sie können Aktionsgruppen für die neuen Warnungsregeln der intelligenten Erkennung erstellen und verwalten, genau wie für jede andere Azure Monitor-Warnungsregel. 

### <a name="managing-alert-rule-settings-using-azure-resource-manager-templates"></a>Verwalten von Einstellungen für Warnungsregeln mithilfe von Azure Resource Manager-Vorlagen

Nach Abschluss der Migration können Sie Azure Resource Manager-Vorlagen verwenden, um Einstellungen für Warnungsregeln der intelligenten Erkennung zu konfigurieren.

> [!NOTE]
> Nach Durchführung der Migration müssen die Einstellungen für die intelligente Erkennung mithilfe von Warnungsregelvorlagen für die intelligente Erkennung konfiguriert werden und können nicht mehr mit [Application Insights Resource Manager-Vorlagen](../app/proactive-arm-config.md#smart-detection-rule-configuration) konfiguriert werden.

Dieses Beispiel für eine Azure Resource Manager-Vorlage veranschaulicht, wie die Warnungsregel **Beeinträchtigung der Antwortlatenz** im Zustand **Aktiviert** mit dem Schweregrad 2 konfiguriert wird.
* Die intelligente Erkennung ist ein globaler Dienst, deshalb werden Regeln am **globalen** Standort erstellt.
* Die Eigenschaft „id“ sollte sich entsprechend der konfigurierten Erkennung ändern. Es muss einer der folgenden Werte verwendet werden:

  - **FailureAnomaliesDetector**
  - **RequestPerformanceDegradationDetector**
  - **DependencyPerformanceDegradationDetector**
  - **ExceptionVolumeChangedDetector**
  - **TraceSeverityDetector**  
  - **MemoryLeakDetector**
 
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Response Latency Degradation - my-app",
            "location": "global", 
            "properties": {
                  "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT24H",
                  "detector": {
                  "id": "RequestPerformanceDegradationDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu Warnungen in Azure](./alerts-overview.md)
- [Weitere Informationen zur intelligenten Erkennung in Application Insights](../app/proactive-diagnostics.md)
