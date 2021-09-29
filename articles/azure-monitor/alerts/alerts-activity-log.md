---
title: Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen in Azure Monitor
description: Erstellen von Aktivitätsprotokollwarnungen über das Azure-Portal mit einer Azure Resource Manager-Vorlage und Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 08/12/2021
ms.openlocfilehash: 273f8b3a747685c250958e745ce6a37754859329
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744582"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor  

## <a name="overview"></a>Übersicht

Aktivitätsprotokollwarnungen werden aktiviert, wenn ein neues Aktivitätsprotokollereignis eintritt, das die in der Warnung angegebenen Bedingungen erfüllt.

Diese Warnungen gelten für Azure-Ressourcen und können mit einer Azure Resource Manager-Vorlage erstellt werden. Sie können auch im Azure-Portal erstellt, aktualisiert oder gelöscht werden. Normalerweise erstellen Sie Aktivitätsprotokollwarnungen, um Benachrichtigungen zu erhalten, wenn es für Ressourcen in Ihrem Azure-Abonnement zu spezifischen Änderungen kommt. Warnungen gelten häufig für bestimmte Ressourcengruppen oder Ressourcen. Es kann beispielsweise sein, dass Sie benachrichtigt werden möchten, wenn ein beliebiger virtueller Computer in der Beispielressourcengruppe **myProductionResourceGroup** gelöscht wird. Unter Umständen möchten Sie auch benachrichtigt werden, wenn einem Benutzer unter Ihrem Abonnement neue Rollen zugewiesen werden.

> [!IMPORTANT]
> Warnungen zur Service Health-Benachrichtigung können nicht über die Schnittstelle zur Erstellung von Aktivitätsprotokollwarnungen erstellt werden. Weitere Informationen zum Erstellen und Verwenden von Service Health-Benachrichtigungen finden Sie unter [Empfangen von Aktivitätsprotokollwarnungen zu Service Health-Benachrichtigungen](../../service-health/alerts-activity-log-service-notifications-portal.md).

Beachten Sie beim Erstellen von Warnungsregeln die folgenden Punkte:

- Das Abonnement im Bereich unterscheidet sich nicht von dem Abonnement, unter dem die Warnung erstellt wird.
- Die Kriterien, für die die Warnung konfiguriert wird, müssen die Ereigniskategorie „Ebene“, „Status“, „Aufrufer“, „Ressourcengruppe“, „Ressourcen-ID“ oder „Ressourcentyp“ sein.
- Im JSON-Code für die Warnungskonfiguration gibt es keine Bedingung „anyOf“ oder geschaltete Bedingungen. Grundsätzlich ist nur eine „allOf“-Bedingung ohne weitere „allOf“- oder „anyOf“-Bedingungen zulässig.
- Wenn die Kategorie „Verwaltung“ lautet, müssen Sie in Ihrer Warnung mindestens ein Kriterium der oben genannten Kriterien angeben. Eine Warnung, die jedes Mal aktiviert wird, wenn ein Ereignis in den Aktivitätsprotokollen erstellt wird, kann nicht erstellt werden.
- Warnungen können für Ereignisse in der Kategorie „Warnung“ des Aktivitätsprotokolls nicht erstellt werden.

## <a name="azure-portal"></a>Azure-Portal

Sie können das Azure-Portal verwenden, um Warnungsregeln für das Aktivitätsprotokoll zu erstellen und zu ändern. Die Umgebung ist in ein Aktivitätsprotokoll von Azure integriert, um eine nahtlose Erstellung von Warnmeldungen für bestimmte Ereignisse zu gewährleisten, die für Sie von Interesse sind.
Im Azure-Portal können Sie eine neue Warnungsregel für das Aktivitätsprotokoll entweder über das Blatt „Warnungen“ oder das Blatt „Aktivitätsprotokoll“ in Azure Monitor erstellen. 


### <a name="create-an-alert-rule-from-the-azure-monitor-alerts-blade"></a>Erstellen einer Warnungsregel über das Blatt „Warnungen“ in Azure Monitor

Im folgenden Verfahren wird beschrieben, wie Sie eine Metrikwarnungsregel im Azure-Portal erstellen:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Überwachen**. Das Blatt „Überwachen“ konsolidiert alle Ihre Überwachungseinstellungen und Daten in einer Ansicht.

2. Klicken Sie auf **Warnungen** und dann auf **+ Neue Warnungsregel**.

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-button-new.png" alt-text="Screenshot der Schaltfläche „Neue Warnungsregel“":::
    > [!TIP]
    > Die meisten Ressourcenblätter verfügen auch über **Warnungen** in ihrem Ressourcenmenü (unter **Überwachung**). Sie können auch von dort aus Benachrichtigungen erstellen.

3. Klicken Sie auf **Ziel auswählen** und wählen Sie im geladenen Kontextbereich eine Zielressource aus, für die Sie eine Warnungsregel erstellen möchten. Verwenden Sie die Dropdownlisten **Abonnement** und **Ressourcentyp**, um die zu überwachende Ressource zu finden. Sie können auch die Suchleiste verwenden, um Ihre Ressource zu finden.
    
    > [!NOTE]
    > Als Ziel können Sie ein gesamtes Abonnement, eine Ressourcengruppe oder eine bestimmte Ressource auswählen. Wenn Sie ein Abonnement oder eine Ressourcengruppe als Ziel und zudem einen Ressourcentyp ausgewählt haben, gilt die Regel für alle Ressourcen dieses Typs innerhalb des ausgewählten Abonnements oder einer Ressourcengruppe. Wenn Sie eine bestimmte Zielressource ausgewählt haben, gilt die Regel nur für diese Ressource. Sie können mithilfe der Zielauswahl nicht mehrere Abonnements, Ressourcengruppen oder Ressourcen explizit auswählen. 

4. Wenn die ausgewählte Ressource Aktivitätsprotokollvorgänge aufweist, für die Sie Warnungen erstellen können, wird für **Verfügbare Signale** unten rechts der Eintrag „Aktivitätsprotokoll“ angezeigt. Die vollständige Liste der für Aktivitätsprotokollwarnungen unterstützten Ressourcentypen finden Sie in diesem [Artikel](../../role-based-access-control/resource-provider-operations.md).

    :::image type="content" source="media/alerts-activity-log/select-target-new.png" alt-text="Screenshot des Blatts für die Zielauswahl" lightbox="media/alerts-activity-log/select-target-new.png":::

5. Nachdem Sie eine Zielressource ausgewählt haben, klicken Sie auf **Bedingung hinzufügen**.

6. Sie sehen eine Liste der für die Ressource unterstützten Signale, einschließlich der Signale aus verschiedenen Kategorien für **Aktivitätsprotokoll**. Wählen Sie das Aktivitätsprotokollsignal bzw. den Aktivitätsprotokollvorgang aus, für das/den Sie eine Warnung erstellen möchten.

7. Es wird ein Diagramm für den Aktivitätsprotokollvorgang für die letzten sechs Stunden angezeigt. Verwenden Sie die Dropdownliste **Diagrammzeitraum**, um einen längeren Verlauf für den Vorgang anzuzeigen.

8. Unter **Warnungslogik** können Sie optional weitere Filterkriterien definieren:

    - **Ereignisstufe**: Der Schweregrad des Ereignisses: _Ausführlich_, _Information_, _Warnung_, _Fehler_ oder _Kritisch_.
    - **Status:** Der Status des Ereignisses: _Gestartet_, _Fehlgeschlagen_ oder _Erfolgreich_.
    - **Ereignis initiiert von:** Wird auch als „Aufrufer“ bezeichnet. Die E-Mail-Adresse oder der Azure Active Directory-Bezeichner des Benutzers, der den Vorgang durchgeführt hat.

    > [!NOTE]
    >   Um hochwertige und effektive Regeln zu erhalten, wird Folgendes empfohlen: Wenn der Warnungsbereich ein gesamtes Abonnement umfasst und das ausgewählte Signal auf „Alle Verwaltungsvorgänge“ festgelegt ist, müssen Sie beim Definieren der Bedingung eine der Dropdownlisten für die Warnungslogik ausfüllen („Ereignisstufe“, „Status“ oder „Initiiert von“), damit die Regel spezifischer wird.
        
9. Klicken Sie auf **Fertig**.

    :::image type="content" source="media/alerts-activity-log/condition-selected-new.png" alt-text="Screenshot des Blatts für die Bedingungsauswahl" lightbox="media/alerts-activity-log/condition-selected-new.png":::

10. Geben Sie **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.

    > [!NOTE]
    >   Der Warnungsschweregrad für Aktivitätsprotokollwarnungen kann derzeit nicht vom Benutzer konfiguriert werden und ist standardmäßig auf „Sev4“ festgelegt.

11. Fügen Sie der Warnung eine Aktionsgruppe hinzu, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.

12. Klicken Sie auf **Fertig**, um die Warnungsregel für das Aktivitätsprotokoll zu speichern.
     
     
### <a name="create-an-alert-rule-from-the-azure-monitor-activity-log-blade"></a>Erstellen einer Warnungsregel über das Blatt „Aktivitätsprotokoll“ in Azure Monitor

Eine alternative Möglichkeit zum Erstellen einer Aktivitätsprotokollwarnung besteht darin, mit einem bereits aufgetretenen Aktivitätsprotokollereignis über das [Aktivitätsprotokoll im Azure-Portal](../essentials/activity-log.md#view-the-activity-log) zu beginnen. 

1. Auf dem Blatt **Azure Monitor – Aktivitätsprotokoll** können Sie das gewünschte Ereignis filtern oder finden und dann eine Warnung für zukünftige ähnliche Ereignisse mit der Schaltfläche **Aktivitätsprotokollwarnung hinzufügen** erstellen. 

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png" alt-text="Screenshot der Erstellung einer Warnungsregel über ein Aktivitätsprotokollereignis" lightbox="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png":::

2. Das Blatt zum Erstellen einer Warnungsregel wird geöffnet, wobei der Bereich und die Bedingung der Warnungsregel bereits entsprechend dem zuvor ausgewählten Aktivitätsprotokollereignis ausgefüllt sind. Sie können den Bereich und die Bedingung in dieser Phase bei Bedarf bearbeiten und ändern. Beachten Sie, dass der genaue Bereich und die Bedingung für die neue Regel standardmäßig unverändert aus den ursprünglichen Ereignisattributen kopiert werden. Beispielsweise sind die genaue Ressource, für die das Ereignis aufgetreten ist, und der Name des spezifischen Benutzers/Diensts, der das Ereignis initiiert hat, standardmäßig in der neuen Warnungsregel enthalten. Wenn Sie die Warnungsregel allgemeiner gestalten möchten, müssen Sie den Bereich und die Bedingung entsprechend ändern, wie es in den Schritten 3 bis 9 oben erläutert ist. 

3. Befolgen Sie dann die Schritte 10 bis 12 wie zuvor gezeigt.
    
### <a name="view-and-manage-in-the-azure-portal"></a>Anzeigen und Verwalten im Azure-Portal

1. Wählen Sie im Azure-Portal die Option **Überwachen** > **Warnungen** aus. Wählen Sie **Warnungsregeln verwalten** in der oberen linken Ecke des Fensters aus.

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-button-new.png" alt-text="Screenshot der Schaltfläche „Warnungsregeln verwalten“":::
    
    Die Liste mit den verfügbaren Regeln wird angezeigt.

2. Filtern oder suchen Sie nach der zu ändernden Aktivitätsprotokollregel.

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-new.png" alt-text="Screenshot des Blatts zur Verwaltung von Warnungsregeln" lightbox="media/alerts-activity-log/manage-alert-rules-new.png":::

    Sie können die verfügbaren Filter _Abonnement_, _Ressourcengruppe_, _Ressource_, _Signaltyp_ oder _Status_ verwenden, um die Aktivitätsregel zu ermitteln, die Sie bearbeiten möchten.
 
3. Wählen Sie die Regel aus, und doppelklicken Sie darauf, um die Regeloptionen zu bearbeiten. Nehmen Sie die erforderlichen Änderungen vor, und wählen Sie dann **Speichern** aus. 

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
Um eine Aktivitätsprotokoll-Warnungsregel mithilfe einer Azure Resource Manager-Vorlage zu erstellen, müssen Sie eine Ressource des Typs `microsoft.insights/activityLogAlerts` erstellen. Anschließend tragen Sie alle zugehörigen Eigenschaften ein. Hier sehen Sie eine Vorlage, mit der eine Aktivitätsprotokoll-Warnungsregel erstellt wird:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Der vorherige JSON-Beispielcode kann z. B. als „sampleActivityLogAlert.json“ gespeichert und mit [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md) bereitgestellt werden.

> [!NOTE]
> 
> Beachten Sie, dass die höchste Ebene, auf der Aktivitätsprotokollwarnungen definiert werden können, das Abonnement ist.
> Das bedeutet, dass es keine Möglichkeit gibt, eine Warnung für mehrere Abonnements zu definieren. Daher sollte die Definition eine Warnung pro Abonnement umfassen.

Bei den folgenden Feldern handelt es sich um die Optionen, die Sie in der Azure Resource Manager-Vorlage für die Bedingungsfelder verwenden können. Beachten Sie, dass „Resource Health“, „Advisor“ und „Service Health“ zusätzliche Eigenschaftenfelder für die jeweiligen speziellen Felder umfassen. 
1. resourceId:  Die Ressourcen-ID der betroffenen Ressource in dem Aktivitätsprotokollereignis, für das die Warnung generiert werden soll.
2. category: Die Kategorie des Aktivitätsprotokollereignisses. Beispiel: „Administrative“ (Verwaltung), „ServiceHealth“ (Dienstintegrität), „ResourceHealth“ (Ressourcenintegrität), „Autoscale“ (Autoskalierung), „Security“ (Sicherheit), „Recommendation“ (Empfehlung), „Policy“ (Richtlinie).
3. caller: Die E-Mail-Adresse oder der Azure Active Directory-Bezeichner des Benutzers, der den Vorgang des Aktivitätsprotokollereignisses durchgeführt hat.
4. level: Die Ebene der Aktivität in dem Aktivitätsprotokollereignis, für das die Warnung generiert werden soll. Beispiel: „Critical“ (Kritisch), „Error“ (Fehler), „Warning“ (Warnung), „Informational“ (Information) oder „Verbose“ (Ausführlich).
5. operationName: Der Name des Vorgangs im Aktivitätsprotokollereignis. Beispiel: Microsoft.Resources/deployments/write
6. resourceGroup: Der Name der Ressourcengruppe für die betroffene Ressource im Aktivitätsprotokollereignis.
7. resourceProvider: [Erläuterung zu Azure-Ressourcenanbietern und -typen](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Eine Liste, die Ressourcenanbieter zu Azure-Diensten zuordnet, finden Sie unter [Ressourcenanbieter für Azure-Dienste](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: Eine Zeichenfolge, die den Status des Vorgangs im Aktivitätsereignis beschreibt. Beispiel: „Started“ (Gestartet), „In Progress“ (In Bearbeitung), „Succeeded“ (Erfolgreich), „Failed“ (Fehler), „Active“ (Aktiv), „Resolved“ (Gelöst)
9. subStatus: Üblicherweise der HTTP-Statuscode des entsprechenden REST-Aufrufs, kann aber auch weitere Zeichenfolgen zur Beschreibung eines untergeordneten Status enthalten.   Beispiel: OK (HTTP-Statuscode: 200), Erstellt (HTTP-Statuscode: 201), Akzeptiert (HTTP-Statuscode: 202), Kein Inhalt (HTTP-Statuscode: 204), Ungültige Anforderung (HTTP-Statuscode: 400), Nicht gefunden (HTTP-Statuscode: 404), Konflikt (HTTP-Statuscode: 409), Interner Serverfehler (HTTP-Statuscode: 500), Dienst nicht verfügbar (HTTP-Statuscode: 503), Gatewaytimeout (HTTP-Statuscode: 504).
10. resourceType: Der Typ der Ressource, die vom Ereignis betroffen war. Beispiel: Microsoft.Resources/deployments

Beispiel:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Weitere Informationen zu den Aktivitätsprotokollfeldern finden Sie [hier](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Es kann bis zu 5 Minuten dauern, bis die neue Warnungsregel des Aktivitätsprotokolls aktiv wird.

## <a name="rest-api"></a>REST-API 
Die [Azure Monitor-Aktivitätsprotokollwarnungen-API](/rest/api/monitor/activitylogalerts) ist eine Rest-API. Sie ist vollständig mit der Azure Resource Manager-Rest-API kompatibel. Sie kann über PowerShell mit dem Resource Manager-Cmdlet oder der Azure CLI verwendet werden.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Bereitstellen der Resource Manager-Vorlage mit PowerShell
Um PowerShell zum Bereitstellen der Resource Manager-Beispielvorlage zu verwenden, die im vorherigen Abschnitt [Azure Resource Manager-Vorlage](#azure-resource-manager-template) gezeigt wurde, verwenden Sie den folgenden Befehl:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Dabei enthält die Datei „sampleActivityLogAlert.parameters.json“ die Werte für die Parameter, die für die Erstellung der Warnungsregeln erforderlich sind.

### <a name="use-activity-log-powershell-cmdlets"></a>Verwenden von PowerShell-Aktivitätsprotokoll-Cmdlets

In Aktivitätsprotokollwarnungen stehen folgende dedizierte PowerShell-Cmdlets zur Verfügung:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): Erstellt eine neue Aktivitätsprotokollwarnung oder aktualisiert eine vorhandene Aktivitätsprotokollwarnung.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): Ruft mindestens eine Aktivitätsprotokoll-Warnungsressource ab.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): Aktiviert eine vorhandene Aktivitätsprotokollwarnung und legt deren Tags fest.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): Deaktiviert eine vorhandene Aktivitätsprotokollwarnung und legt deren Tags fest.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): Entfernt eine Aktivitätsprotokollwarnung.

## <a name="azure-cli"></a>Azure CLI

Unter [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) stehen dedizierte Azure CLI-Befehle zum Verwalten von Aktivitätsprotokoll-Warnungsregeln zur Verfügung.

Verwenden Sie für die Erstellung einer neuen Warnungsregel des Aktivitätsprotokolls die folgenden Befehle in der angegebenen Reihenfolge:

1. [az monitor activity-log alert create:](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create) Erstellen einer neuen Ressource für Warnungsregeln des Aktivitätsprotokolls.
2. [az monitor activity-log alert scope:](/cli/azure/monitor/activity-log/alert/scope) Hinzufügen des Bereichs für die erstellte Warnungsregel des Aktivitätsprotokolls.
3. [az monitor activity-log alert action-group:](/cli/azure/monitor/activity-log/alert/action-group) Hinzufügen einer Aktionsgruppe zur Warnungsregel des Aktivitätsprotokolls.

Zum Abrufen einer Ressource für Warnungsregeln des Aktivitätsprotokolls verwenden Sie den Azure CLI-Befehl [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Zum Anzeigen aller Ressourcen für Warnungsregeln des Aktivitätsprotokolls in einer Ressourcengruppe verwenden Sie den Befehl [az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Ressourcen für Warnungsregeln des Aktivitätsprotokolls können mit dem Azure CLI-Befehl [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Webhookschema für Aktivitätsprotokolle](./activity-log-alerts-webhook.md).
- Lesen Sie eine [Übersicht über Aktivitätsprotokolle](./activity-log-alerts.md).
- Weitere Informationen zu [Aktionsgruppen](./action-groups.md).  
- Weitere Informationen zu [Dienstintegritätsbenachrichtigungen](../../service-health/service-notifications.md).