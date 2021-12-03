---
title: Verschieben eines Log Analytics-Arbeitsbereichs in eine andere Azure-Region über das Azure-Portal
description: Verwenden Sie eine Azure Resource Manager-Vorlage, um einen Log Analytics-Arbeitsbereich über das Azure-Portal von einer Azure-Region in eine andere zu verschieben.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 01d7852e514931a1546a9ab66c3b582bea115f2b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319622"
---
# <a name="move-a-log-analytics-workspace-to-another-region-by-using-the-azure-portal"></a>Verschieben eines Log Analytics-Arbeitsbereichs in eine andere Region über das Azure-Portal

Es gibt verschiedene Szenarien, in denen Sie Ihren vorhandenen Log Analytics-Arbeitsbereich von einer Region in eine andere verschieben möchten oder müssen. Ein Beispiel: Log Analytics ist seit Kurzem in einer Region verfügbar, in der die meisten Ihrer Ressourcen gespeichert sind. Durch eine Verschiebung läge der Arbeitsbereich in geringerer Entfernung, sodass Sie Gebühren für ausgehende Daten einsparen könnten. Oder Sie möchten Ihren Arbeitsbereich aufgrund von Anforderungen an die Datenhoheit in eine neu hinzugefügte Region verschieben.

Ein Log Analytics-Arbeitsbereich kann nicht von einer Region in eine andere verschoben werden. Sie können aber mithilfe einer Azure Resource Manager-Vorlage die Arbeitsbereichsressource und die zugehörigen Ressourcen exportieren. Anschließend können Sie die Ressourcen in einer anderen Region stagen, indem Sie den Arbeitsbereich in eine Vorlage exportieren, die Parameter so ändern, dass sie der Zielregion entsprechen, und die Vorlage dann in der neuen Region bereitstellen. Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). 

Eine Arbeitsbereichsumgebung kann komplex sein und verbundene Quellen, verwaltete Lösungen, verknüpfte Dienste, Warnungen und Abfragepakete enthalten. Nicht alle Ressourcen können in eine Resource Manager-Vorlage exportiert werden, und für einige Ressourcen ist beim Verschieben eines Arbeitsbereichs eine separate Konfiguration erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

- Um die Arbeitsbereichskonfiguration in eine Vorlage zu exportieren, die in einer anderen Region bereitgestellt werden kann, benötigen Sie die Rolle [Log Analytics-Mitwirkender](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) oder [Mitwirkender an der Überwachung](../../role-based-access-control/built-in-roles.md#monitoring-contributor) bzw. höhere Berechtigungen.

- Identifizieren Sie alle Ressourcen, die Ihrem Arbeitsbereich derzeit zugeordnet sind, darunter:
  - *Verbundene Agents*: Geben Sie **Protokolle** in Ihren Arbeitsbereich ein, und fragen Sie die Tabelle [Heartbeat](../insights/solution-agenthealth.md#azure-monitor-log-records) ab, um verbundene Agents aufzulisten.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - *Diagnoseeinstellungen*: Ressourcen können Protokolle an die Azure-Diagnose oder an dedizierte Tabellen in Ihrem Arbeitsbereich senden. Geben Sie **Protokolle** in Ihren Arbeitsbereich ein, und führen Sie diese Abfrage für Ressourcen aus, die Daten an die Tabelle `AzureDiagnostics` senden:

    ```kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | summarize by  ResourceProvider , ResourceType, Resource
    | sort by ResourceProvider, ResourceType
    ```

    Führen Sie diese Abfrage für Ressourcen aus, die Daten an dedizierte Tabellen senden:

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | where isnotnull(_ResourceId)
    | extend ResourceProvider = split(_ResourceId, '/')[6]
    | where ResourceProvider !in ('microsoft.compute', 'microsoft.security')
    | extend ResourceType = split(_ResourceId, '/')[7]
    | extend Resource = split(_ResourceId, '/')[8]
    | summarize by tostring(ResourceProvider) , tostring(ResourceType), tostring(Resource)
    | sort by ResourceProvider, ResourceType
    ```

  - *Installierte Lösungen*: Wählen Sie im Navigationsbereich des Arbeitsbereichs **Lösungen** aus, um eine Liste der installierten Lösungen anzuzeigen.
  - *Datensammler-API*: Daten, die über die [Datensammler-API](../logs/data-collector-api.md) eingehen, werden in benutzerdefinierten Protokolltabellen gespeichert. Wählen Sie für eine Liste benutzerdefinierter Protokolltabellen im Navigationsbereich des Arbeitsbereichs die Option **Protokolle** und dann im Schemabereich **Benutzerdefiniertes Protokoll** aus.
  - *Verknüpfte Dienste*: Der Arbeitsbereich kann verknüpfte Dienste aufweisen, z. B. für abhängige Ressourcen wie Azure Automation-Konten, Speicherkonten oder dedizierte Cluster. Entfernen Sie verknüpfte Dienste aus Ihrem Arbeitsbereich. Konfigurieren Sie sie manuell im Zielarbeitsbereich neu.
  - *Warnungen*: Um Warnungen aufzulisten, wählen Sie im Navigationsbereich Ihres Arbeitsbereichs die Option **Warnungen** und dann in der Symbolleiste die Option **Warnungsregeln verwalten** aus. Warnungen in Arbeitsbereichen, die nach dem 1 Juni 2019 oder in Arbeitsbereichen erstellt wurden, für die ein [Upgrade von der Log Analytics-Warnungs-API auf die aktuelle ScheduledQueryRules-API](../alerts/alerts-log-api-switch.md) durchgeführt wurde, können in die Vorlage einbezogen werden. 
  
     Sie können [überprüfen, ob die ScheduledQueryRules-API für Warnungen in Ihrem Arbeitsbereich verwendet wird](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Alternativ können Sie Warnungen manuell im Zielarbeitsbereich konfigurieren.
  - *Abfragepakete*: Ein Arbeitsbereich kann mehreren Abfragepaketen zugeordnet werden. Um Abfragepakete in Ihrem Arbeitsbereich zu identifizieren, wählen Sie **Protokolle** im Navigationsbereich des Arbeitsbereichs sowie **Abfragen** im linken Bereich aus. Anschließend wählen Sie die Auslassungspunkte rechts neben dem Suchfeld aus. Auf der rechten Seite öffnet sich ein Dialogfeld mit den ausgewählten Abfragepaketen. Wenn sich Ihre Abfragepakete in derselben Ressourcengruppe befinden wie der zu verschiebende Arbeitsbereich, können Sie ihn in diese Migration einschließen.
- Vergewissern Sie sich, dass Sie mit Ihrem Azure-Abonnement Log Analytics-Arbeitsbereiche in der Zielregion erstellen können.

## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
In den folgenden Verfahren wird gezeigt, wie Sie den Arbeitsbereich auf das Verschieben mit einer Resource Manager-Vorlage vorbereiten und den Arbeitsbereich dann über das Portal in die Zielregion verschieben. Führen Sie die Verfahren der Reihe nach durch.

> [!NOTE]
> Nicht alle Ressourcen können über eine Vorlage exportiert werden. Sie müssen diese separat konfigurieren, nachdem der Arbeitsbereich in der Zielregion erstellt wurde.

### <a name="select-resource-groups-and-edit-parameters"></a>Auswählen von Ressourcengruppen und Bearbeiten von Parametern

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressourcengruppen** aus.
1. Suchen Sie die Ressourcengruppe, die Ihren Arbeitsbereich enthält, und wählen Sie sie aus.
1. Aktivieren Sie das Kontrollkästchen **Ausgeblendete Typen anzeigen**, um eine Warnungsressource anzuzeigen.
1. Wählen Sie den Filter **Typ** aus. Wählen Sie **Log Analytics-Arbeitsbereich**, **Lösung**, **SavedSearches**, **microsoft.insights/scheduledqueryrules**, **defaultQueryPack** und andere auf den Arbeitsbereich bezogenen Ressourcen aus, über die Sie verfügen (z. B. ein Automation-Konto). Wählen Sie dann **Anwenden** aus.
1. Wählen Sie den Arbeitsbereich, die Lösungen, die gespeicherten Suchen, die Warnungen, die Abfragepakete und andere arbeitsbereichsbezogene Ressourcen aus, über die Sie verfügen (z. B. ein Automation-Konto). Wählen Sie dann auf der Symbolleiste **Vorlage exportieren** aus.
    
    > [!NOTE]
    > Microsoft Sentinel kann nicht mit einer Vorlage exportiert werden. Sie müssen für [Sentinel das Onboarding](../../sentinel/quickstart-onboard.md) in einen Zielarbeitsbereich durchführen.
   
1. Wählen Sie auf der Symbolleiste **Bereitstellen** aus, um die Vorlage für die Bereitstellung zu bearbeiten und vorzubereiten.
1. Wählen Sie auf der Symbolleiste **Parameter bearbeiten** aus, um die Datei *parameters.json* im Online-Editor zu öffnen.
1. Ändern Sie die `value`-Eigenschaft unter `parameters`, um die Parameter zu bearbeiten. Hier sehen Sie ein Beispiel:

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

1. Wählen Sie im Editor die Option **Speichern** aus.

### <a name="edit-the-template"></a>Bearbeiten der Vorlage

1. Wählen Sie auf der Symbolleiste **Vorlage bearbeiten** aus, um die Datei *template.json* im Online-Editor zu öffnen.
1. Zum Bearbeiten der Zielregion, in der der Log Analytics-Arbeitsbereich bereitgestellt werden soll, ändern Sie im Online-Editor unter `resources` die Eigenschaft `location`. 

   Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Datenresidenz in Azure](https://azure.microsoft.com/global-infrastructure/locations/). Der Code für eine Region ist der Regionsname ohne Leerzeichen. Beispiel: **USA, Mitte** sollte `centralus` sein.
1. Entfernen Sie Ressourcen verknüpfter Dienste (`microsoft.operationalinsights/workspaces/linkedservices`), sofern diese in der Vorlage vorhanden sind. Sie sollten diese Ressourcen im Zielarbeitsbereich manuell neu konfigurieren.

   Die folgende Beispielvorlage enthält den Arbeitsbereich, die gespeicherte Suche, Lösungen, Warnungen und das Abfragepaket:

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
          "apiVersion": "2021-08-01",
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

1. Wählen Sie im Online-Editor die Option **Speichern** aus.

### <a name="deploy-the-workspace"></a>Bereitstellen des Arbeitsbereichs

1. Wählen Sie **Abonnement** aus, um das Abonnement auszuwählen, in dem der Zielarbeitsbereich bereitgestellt wird.
1. Wählen Sie **Ressourcengruppe** aus, um die Ressourcengruppe auszuwählen, in der der Zielarbeitsbereich bereitgestellt wird. Sie können **Neu erstellen** auswählen, um eine neue Ressourcengruppe für den Zielarbeitsbereich zu erstellen.
1. Vergewissern Sie sich, dass **Region** auf den Zielstandort festgelegt ist, an dem die Netzwerksicherheitsgruppe bereitgestellt werden soll.
1. Wählen Sie die Schaltfläche **Überprüfen + erstellen** aus, um Ihre Vorlage zu überprüfen.
1. Wählen Sie **Erstellen** aus, um den Arbeitsbereich und die ausgewählte Ressource in der Zielregion bereitzustellen.
1. Ihr Arbeitsbereich, einschließlich ausgewählter Ressourcen, wird jetzt in der Zielregion bereitgestellt. Sie können die verbleibende Konfiguration im Arbeitsbereich vervollständigen, um die Funktionalität mit dem ursprünglichen Arbeitsbereich zu koppeln.
   - *Verbinden von Agents*: Verwenden Sie eine der verfügbaren Optionen (einschließlich Datensammlungsregeln), um die benötigten Agents für VMs und VM-Skalierungsgruppen zu konfigurieren und den neuen Zielarbeitsbereich als Ziel anzugeben.
   - *Diagnoseeinstellungen*: Aktualisieren Sie die Diagnoseeinstellungen in identifizierten Ressourcen, wobei der Zielarbeitsbereich das Ziel darstellt.
   - *Installieren von Lösungen*: Einige Lösungen, z. B. [Microsoft Sentinel](../../sentinel/quickstart-onboard.md), erfordern ein bestimmtes Onboardingverfahren und sind nicht in der Vorlage enthalten. Führen Sie für diese Lösungen ein separates Onboarding im neuen Arbeitsbereich durch.
   - *Konfigurieren der Datensammler-API*: Konfigurieren Sie Datensammler-API-Instanzen, um Daten an den Zielarbeitsbereich zu senden.
   - *Konfigurieren von Warnungsregeln*: Wenn keine Warnungen in die Vorlage exportiert wurden, müssen Sie diese im Zielarbeitsbereich manuell konfigurieren.
1. Stellen Sie sicher, dass neue Daten nicht im ursprünglichen Arbeitsbereich erfasst werden. Führen Sie die folgende Abfrage in Ihrem ursprünglichen Arbeitsbereich aus, und stellen Sie fest, dass nach der Migration keine Erfassung erfolgt:

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | summarize max(TimeGenerated) by Type
    ```

Nachdem die Datenquellen mit dem Zielarbeitsbereich verbunden sind, werden die erfassten Daten im Zielarbeitsbereich gespeichert. Ältere Daten verbleiben im ursprünglichen Arbeitsbereich und unterliegen der Aufbewahrungsrichtlinie. Sie können eine [arbeitsbereichsübergreifende Abfrage](./cross-workspace-query.md#performing-a-query-across-multiple-resources) ausführen. Wenn beiden Arbeitsbereichen derselbe Name zugewiesen wurde, verwenden Sie einen qualifizierten Namen (*subscriptionName/resourceGroup/componentName*) in der Arbeitsbereichsreferenz.

Hier ist ein Beispiel für eine Abfrage in zwei Arbeitsbereichen mit demselben Namen:

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>Verwerfen

Wenn Sie den Quellarbeitsbereich verwerfen möchten, löschen Sie die exportierten Ressourcen oder die Ressourcengruppe, die diese Ressourcen enthält:

1. Wählen Sie die Zielressourcengruppe im Azure-Portal aus.
1. Wechseln Sie zur **Übersichtsseite**:
   
   - Wenn Sie eine neue Ressourcengruppe für diese Bereitstellung erstellt haben, wählen Sie auf der Symbolleiste **Ressourcengruppe löschen** aus, um die Ressourcengruppe zu löschen.
   - Wenn die Vorlage für eine bestehende Ressourcengruppe bereitgestellt wurde, wählen Sie die Ressourcen aus, die mit der Vorlage bereitgestellt wurden, und wählen Sie dann auf der Symbolleiste **Löschen** aus, um die ausgewählten Ressourcen zu löschen.

## <a name="clean-up"></a>Bereinigen

Während neue Daten in Ihren neuen Arbeitsbereich erfasst werden, stehen ältere Daten im ursprünglichen Arbeitsbereich weiterhin für Abfragen zur Verfügung und unterliegen der im Arbeitsbereich definierten Aufbewahrungsrichtlinie. Es wird empfohlen, den ursprünglichen Arbeitsbereich so lange beizubehalten, wie Sie ältere Daten zur [arbeitsbereichübergreifenden Abfrage](./cross-workspace-query.md#performing-a-query-across-multiple-resources) benötigen. 

Wenn Sie den Zugriff auf ältere Daten im ursprünglichen Arbeitsbereich nicht mehr benötigen:

1. Wählen Sie die ursprüngliche Ressourcengruppe im Azure-Portal aus. 
1. Wählen Sie alle zu entfernenden Ressourcen aus, und klicken Sie dann auf der Symbolleiste auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Log Analytics-Arbeitsbereich von einer Region in eine andere verschoben und die Quellressourcen bereinigt. Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie in folgenden Artikeln:

- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../../site-recovery/azure-to-azure-tutorial-migrate.md)
