---
title: Verschieben eines Log Analytics-Arbeitsbereichs in eine andere Azure-Region über das Azure-Portal
description: Verwenden Sie eine Azure Resource Manager-Vorlage, um einen Log Analytics-Arbeitsbereich über das Azure-Portal von einer Azure-Region in eine andere zu verschieben.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: dfb34489ccba60890c7cc7066691ec9a975542b8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446356"
---
# <a name="move-log-analytics-workspace-to-another-region-using-the-azure-portal"></a>Verschieben eines Log Analytics-Arbeitsbereichs in eine andere Region über das Azure-Portal

Es gibt verschiedene Szenarien, in denen Sie Ihren vorhandenen Log Analytics-Arbeitsbereich von einer Region in eine andere verschieben möchten oder müssen. Ein Beispiel: Log Analytics ist seit Kurzem in einer Region verfügbar, in der die meisten Ihrer Ressourcen gespeichert sind. Durch eine Verschiebung läge der Arbeitsbereich in geringerer Entfernung, sodass Sie Gebühren für ausgehende Daten einsparen könnten. Oder Sie möchten Ihren Arbeitsbereich aufgrund von Anforderungen an die Datenhoheit in eine neu hinzugefügte Region verschieben.

Ein Log Analytics-Arbeitsbereich kann nicht von einer Region in eine andere verschoben werden. Sie können jedoch mithilfe einer Azure Resource Manager-Vorlage die Arbeitsbereichsressource und die zugehörigen Ressourcen exportieren. Anschließend können Sie die Ressourcen in einer anderen Region stagen, indem Sie den Arbeitsbereich in eine Vorlage exportieren, die Parameter so ändern, dass sie der Zielregion entsprechen, und die Vorlage dann in der neuen Region bereitstellen. Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Die Arbeitsbereichsumgebung kann komplex sein und verbundene Quellen, verwaltete Lösungen, verknüpfte Dienste, Warnungen und Abfragepakete enthalten. Nicht alle Ressourcen können in eine Resource Manager-Vorlage exportiert werden, und für einige Ressourcen ist beim Verschieben eines Arbeitsbereichs eine separate Konfiguration erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

- Um die Arbeitsbereichskonfiguration in eine Vorlage zu exportieren, die in einer anderen Region bereitgestellt werden kann, benötigen Sie entweder die Rolle [Log Analytics-Mitwirkender](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) oder [Mitwirkender an der Überwachung](../../role-based-access-control/built-in-roles.md#monitoring-contributor) bzw. höhere Berechtigungen.

- Identifizieren Sie alle Ressourcen, die ihrem Arbeitsbereich derzeit zugeordnet sind, darunter:
  - Verbundene Agents: Geben Sie *Protokolle* in Ihren Arbeitsbereich ein, und fragen Sie die Tabelle [Heartbeat](../insights/solution-agenthealth.md#heartbeat-records) ab, um verbundene Agents aufzulisten.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - Installierte Lösungen: Klicken Sie im Navigationsbereich des Arbeitsbereichs auf **Lösungen**, um eine Liste der installierten Lösungen anzuzeigen.
  - Datensammler-API: Daten, die über die [Datensammler-API](../logs/data-collector-api.md) eingehen, werden in benutzerdefinierten Protokolltabellen gespeichert. Klicken Sie im Navigationsbereich des Arbeitsbereichs auf ***Protokolle** und dann im Schemabereich auf _ *Benutzerdefiniertes Protokoll**, um eine Liste mit benutzerdefinierten Protokolltabellen anzuzeigen.
  - Verknüpfte Dienste: Der Arbeitsbereich kann verknüpfte Dienste aufweisen, zum Beispiel für abhängige Ressourcen wie etwa Automation-Konten, Speicherkonten oder dedizierte Cluster. Entfernen Sie verknüpfte Dienste aus Ihrem Arbeitsbereich. Diese müssen im Zielarbeitsbereich manuell neu konfiguriert werden.
  - Warnungen: Klicken Sie m Navigationsbereich Ihres Arbeitsbereichs auf **Warnungen** und dann auf der Symbolleiste auf **Warnungsregeln verwalten**, um Warnungen aufzulisten. Warnungen in Arbeitsbereichen, die nach dem 1. Juni 2019 erstellt wurden, oder in Arbeitsbereichen, für die ein [Upgrade von der Log Analytics-Legacywarnungs-API auf die aktuelle Protokollwarnungs-API](../alerts/alerts-log-api-switch.md) durchgeführt wurde, können in die Vorlage einbezogen werden. Sie können [überprüfen, ob die scheduledQueryRules-API für Warnungen in Ihrem Arbeitsbereich verwendet wird](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Alternativ können Sie Warnungen manuell im Zielarbeitsbereich konfigurieren.
  - Abfragepakete: Ein Arbeitsbereich kann mehreren Abfragepaketen zugeordnet werden. Klicken Sie zum Identifizieren von Abfragepaketen in Ihrem Arbeitsbereich im Navigationsbereich des Arbeitsbereichs auf **Protokolle** und im linken Bereich auf **Abfragen**. Klicken Sie dann auf die Auslassungspunkte rechts neben dem Suchfeld, um weitere Einstellungen anzuzeigen. Auf der rechten Seite wird ein Dialogfeld mit dem ausgewählten Abfragepaket geöffnet. Wenn sich Ihre Abfragepakete in derselben Ressourcengruppe befinden wie der zu verschiebende Arbeitsbereich, können Sie ihn in diese Migration einschließen.
- Vergewissern Sie sich, dass Sie mit Ihrem Azure-Abonnement einen Log Analytics-Arbeitsbereich in der Zielregion erstellen können.

## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
In den folgenden Schritten wird gezeigt, wie Sie den Arbeitsbereich auf das Verschieben mit einer Resource Manager-Vorlage vorbereiten und den Arbeitsbereich dann über das Portal in die Zielregion verschieben. Nicht alle Ressourcen können über eine Vorlage exportiert werden. Diese Ressourcen müssen separat konfiguriert werden, sobald der Arbeitsbereich in der Zielregion erstellt wurde.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportieren der Vorlage und Bereitstellen im Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wechseln Sie zu den **Ressourcengruppen**.
2. Suchen Sie nach der Ressourcengruppe, die Ihren Arbeitsbereich enthält, und klicken Sie darauf.
3. Aktivieren Sie das Kontrollkästchen **Ausgeblendete Typen anzeigen**, um Warnungsressourcen anzuzeigen.
4. Klicken Sie auf den Filter „Typ“, und wählen Sie **Log Analytics-Arbeitsbereich**, **Lösung**, **SavedSearches**, **microsoft.insights/scheduledqueryrules** und **defaultQueryPack** als anwendbare Typen aus. Klicken Sie dann auf „Anwenden“.
5. Wählen Sie die Arbeitsbereiche, Lösungen, Warnungen, gespeicherten Suchen und Abfragepakete aus, die Sie verschieben möchten, und klicken Sie dann auf der Symbolleiste auf **Vorlage exportieren**.
    
    > [!NOTE]
    > Sentinel kann nicht mit einer Vorlage exportiert werden, Sie müssen im Zielarbeitsbereich ein [Onboarding von Sentinel durchführen](../../sentinel/quickstart-onboard.md).
   
6. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um die Vorlage für die Bereitstellung zu bearbeiten und vorzubereiten.
7. Klicken Sie auf der Symbolleiste auf **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.
8. Ändern Sie zum Bearbeiten des Parameters Sie Eigenschaft **value** unter **parameters**.

    Beispiel einer Parameterdatei:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

9. Klicken Sie im Editor auf **Speichern**.
10. Klicken Sie auf der Symbolleiste auf **Vorlage bearbeiten**, um die Datei **template.json** im Online-Editor zu öffnen.
11. Zum Bearbeiten der Zielregion, in die der Log Analytics-Arbeitsbereich verschoben werden soll, ändern Sie im Online-Editor unter **resources** die Eigenschaft **location**. Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/). Der Code für eine Region ist der Regionsname ohne Leerzeichen: **Central US** = **centralus**.
12. Entfernen Sie Ressourcen verknüpfter Dienste `microsoft.operationalinsights/workspaces/linkedservices`, sofern diese in der Vorlage vorhanden sind. Diese müssen im Zielarbeitsbereich manuell neu konfiguriert werden.

    Beispiel für eine Vorlage einschließlich Arbeitsbereich, gespeicherter Suche, Lösungen, Warnung und Abfragepaket:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-02-01-preview",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

13. Klicken Sie im Online-Editor auf **Speichern**.
14. Klicken Sie auf **Abonnement**, um das Abonnement auszuwählen, in dem der Zielarbeitsbereich bereitgestellt wird.
16. Klicken Sie auf **Ressourcengruppe**, um die Ressourcengruppe auszuwählen, in der der Zielarbeitsbereich bereitgestellt wird. Sie können auf **Neu erstellen** klicken, um eine neue Ressourcengruppe für den Zielarbeitsbereich zu erstellen.
17. Stellen Sie sicher, dass die **Region** auf den Zielstandort festgelegt ist, an dem Sie den Zielarbeitsbereich bereitstellen möchten.
18. Klicken Sie auf die Schaltfläche **Überprüfen + erstellen**, um Ihre Vorlage zu überprüfen.
19. Klicken Sie auf **Erstellen**, um den Arbeitsbereich und die ausgewählte Ressource in der Zielregion bereitzustellen.
20. Ihr Arbeitsbereich wird jetzt einschließlich ausgewählter Ressourcen in der Zielregion bereitgestellt, und Sie können die verbleibenden Konfigurationsschritte im Arbeitsbereich ausführen, um dieselbe Funktionalität wie im ursprünglichen Arbeitsbereich bereitzustellen.
    - Verbinden von Agents: Verwenden Sie eine der verfügbaren Optionen (einschließlich DCR), um die benötigten Agents für VMs und VM-Skalierungsgruppen zu konfigurieren und den neuen Zielarbeitsbereich als Ziel anzugeben.
    - Installieren von Lösungen: Einige Lösungen, z. B. [Azure Sentinel](../../sentinel/quickstart-onboard.md), erfordern ein bestimmtes Onboardingverfahren und sind nicht in der Vorlage enthalten. Führen Sie für diese Lösungen ein separates Onboarding im neuen Arbeitsbereich durch.
    - Datensammler-API: Konfigurieren Sie Datensammler-API-Instanzen, um Daten an den Zielarbeitsbereich zu senden.
    - Warnungsregeln: Wenn keine Warnungen in die Vorlage exportiert wurden, müssen Sie diese im Zielarbeitsbereich manuell konfigurieren.
21. Stellen Sie sicher, dass neue Daten nicht im ursprünglichen Arbeitsbereich erfasst werden. Führen Sie diese Abfrage in Ihrem ursprünglichen Arbeitsbereich aus, und vergewissern Sie sich, dass dort nach der Migration keine Erfassung erfolgt.

    ```kusto
    search *
    | summarize max(TimeGenerated) by Type
    ```

Die nach der Datenquellenverbindung mit dem Zielarbeitsbereich erfassten Daten werden im Zielarbeitsbereich gespeichert, während ältere Daten im ursprünglichen Arbeitsbereich verbleiben. Sie können [arbeitsbereichsübergreifende](./cross-workspace-query.md#performing-a-query-across-multiple-resources) Abfragen ausführen. Wenn beiden Arbeitsbereichen der gleiche Name zugewiesen wurde, verwenden Sie im Arbeitsbereichsverweis den qualifizierten Namen (*subscriptionName/resourceGroup/componentName*).

Beispiel für eine arbeitsbereichsübergreifende Abfrage, bei der beide Arbeitsbereiche den gleichen Namen aufweisen:

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>Verwerfen

Wenn Sie den Quellarbeitsbereich verwerfen möchten, löschen Sie die exportierten Ressourcen oder die Ressourcengruppe, die diese enthält. Wählen Sie hierzu die Zielressourcengruppe in Azure-Portal aus. Wenn Sie für diese Bereitstellung eine neue Ressourcengruppe erstellt haben, klicken Sie auf der Symbolleiste der Übersichtsseite auf **Ressourcengruppe löschen**. Wenn die Vorlage in einer vorhandenen Ressourcengruppe bereitgestellt wurde, wählen Sie die mit der Vorlage bereitgestellte Ressourcen aus, und klicken Sie auf der Symbolleiste auf **Löschen**.

## <a name="clean-up"></a>Bereinigen

Der ursprüngliche Arbeitsbereich und die vor der Migration darin erfassten Daten verbleiben in der ursprünglichen Region, und die Daten unterliegen der Aufbewahrungsrichtlinie für den Arbeitsbereich. Es wird empfohlen, den ursprünglichen Arbeitsbereich so lange beizubehalten, wie Ihre älteren Daten benötigt werden, um eine [Abfrage über Ziel- und ursprünglichen Arbeitsbereich hinweg](./cross-workspace-query.md#performing-a-query-across-multiple-resources) zu ermöglichen. Wenn Sie nicht länger auf ältere Daten im ursprünglichen Arbeitsbereich oder auf andere Ressourcen in der ursprünglichen Region zugreifen müssen, wählen Sie die ursprüngliche Ressourcengruppe im Azure-Portal aus, markieren Sie alle Ressourcen, die Sie entfernen möchten, und klicken Sie auf der Symbolleiste auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Log Analytics-Arbeitsbereich von einer Region in eine andere verschoben und die Quellressourcen bereinigt. Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:

- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../../site-recovery/azure-to-azure-tutorial-migrate.md)