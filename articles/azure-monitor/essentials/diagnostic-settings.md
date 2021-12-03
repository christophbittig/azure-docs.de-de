---
title: Erstellen von Diagnoseeinstellungen zum Senden von Metriken und Protokollen der Azure Monitor-Plattform an verschiedene Ziele
description: Senden Sie Metriken und Protokolle der Azure Monitor-Plattform mithilfe von Diagnoseeinstellungen an Azure Monitor-Protokolle, Azure Storage oder Azure Event Hubs.
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 6de7ed1acffd7f4c05d912f7f565f4b811ebd357
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402034"
---
# <a name="create-diagnostic-settings-to-send-azure-monitor-platform-logs-and-metrics-to-different-destinations"></a>Erstellen von Diagnoseeinstellungen zum Senden von Metriken und Protokollen der Azure Monitor-Plattform an verschiedene Ziele

Dieser Artikel enthält Details zum Erstellen und Konfigurieren von Diagnoseeinstellungen zum Senden von Azure-Plattformmetriken und Protokollen an verschiedene Ziele.

[Plattformmetriken](./metrics-supported.md) werden automatisch und ohne Konfiguration standardmäßig an [Azure Monitor Metrics](./data-platform-metrics.md) gesendet

[Plattformprotokolle](./platform-logs-overview.md), einschließlich des Azure-Aktivitätsprotokolls und der Ressourcenprotokolle, liefern detaillierte Diagnose- und Prüfungsinformationen für Azure-Ressourcen und die Azure-Plattform, von der sie abhängen. Das Aktivitätsprotokoll existiert für sich allein, kann aber an andere Stellen weitergeleitet werden.  Ressourcenprotokolle werden erst gesammelt, wenn sie an ein Ziel weitergeleitet werden.

Jede Azure-Ressource erfordert eine eigene Diagnoseeinstellung, die folgende Kriterien definiert:

- Quellen: Der Typ der Metrik- und Protokolldaten, die an die in der Einstellung definierten Ziele gesendet werden sollen. Die verfügbaren Typen variieren je nach Ressourcentyp.
- Ziele - Ein oder mehrere Ziele, an die gesendet werden soll.

Mit einer einzelnen Diagnoseeinstellung kann maximal eines der Ziele definiert werden. Wenn Sie Daten an mehrere Ziele eines bestimmten Typs senden möchten (z.B. zwei verschiedene Log Analytics-Arbeitsbereiche), dann erstellen Sie mehrere Einstellungen. Jede Ressource kann bis zu fünf Diagnoseeinstellungen aufweisen.

Das folgende Video führt Sie durch das Routing von Plattformprotokollen mit Diagnoseeinstellungen.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

## <a name="sources"></a>Quellen

Hier sind die Quellenoptionen.

### <a name="metrics"></a>Metriken

Die Einstellung **AlleMetriken** leitet die Plattformmetriken einer Ressource an zusätzliche Ziele weiter. Diese Option ist möglicherweise nicht bei allen Ressourcenanbietern vorhanden.  

### <a name="logs"></a>Protokolle

Bei Protokollen können Sie die Protokollkategorien, die Sie weiterleiten möchten, einzeln auswählen oder eine Kategoriegruppe wählen.

> [!NOTE]
> Kategoriegruppen gelten nicht für Metriken. Nicht für alle Ressourcen sind Kategoriegruppen verfügbar.

**Kategoriegruppen** ermöglichen die dynamische Erfassung von Ressourcenprotokollen auf der Grundlage vordefinierter Gruppierungen anstelle der Auswahl einzelner Protokollkategorien. Microsoft definiert die Gruppierungen, um die Überwachung bestimmter Anwendungsfälle über alle Azure-Dienste hinweg zu erleichtern. Im Laufe der Zeit können die Kategorien in der Gruppe aktualisiert werden, wenn neue Protokolle eingeführt werden oder sich Bewertungen ändern. Wenn Protokollkategorien einer Kategoriegruppe hinzugefügt oder daraus entfernt werden, wird Ihre Protokollsammlung automatisch geändert, ohne dass Sie Ihre Diagnoseeinstellungen aktualisieren müssen.

Wenn Sie Kategoriegruppen verwenden, können Sie: 
- Ressourcenprotokolle nicht mehr individuell auf der Grundlage einzelner Kategorietypen ausgewählt werden
- Aufbewahrungseinstellungen nicht mehr auf Protokolle angewendn, die an Azure Storage gesendet werden

Derzeit gibt es zwei Gruppen von Kategorien:
- **Alle** - Jedes Ressourcenprotokoll, das von der Ressource angeboten wird.
- **Audit** - Alle Ressourcenprotokolle, die Kundeninteraktionen mit Daten oder die Einstellungen des Dienstes aufzeichnen.

## <a name="destinations"></a>Destinations
Plattformprotokolle und -metriken können an die Ziele in der folgenden Tabelle gesendet werden. 

| Destination | BESCHREIBUNG |
|:---|:---|
| [Log Analytics-Arbeitsbereich](../logs/design-logs-deployment.md) | Metriken werden in das Protokollformular konvertiert. Diese Option ist möglicherweise nicht für alle Ressourcentypen verfügbar. Wenn Sie sie an den Azure Monitor Logs-Speicher senden (der über Log Analytics durchsuchbar ist), können Sie sie in Abfragen, Benachrichtigungen und Visualisierungen mit vorhandenen Protokolldaten integrieren.  
| [Azure-Speicherkonto](../../storage/blobs/index.yml) | Das Archivieren von Protokollen und Metriken in einem Azure Storage-Konto ist für die Überwachung, statische Analyse oder Sicherung nützlich. Im Vergleich zu Azure Monitor-Protokollen und einem Log Analytics Arbeitsbereich ist Azure Storage kostengünstiger, und die Protokolle können unbegrenzt aufbewahrt werden.  | 
| [Event Hubs](../../event-hubs/index.yml) | Das Senden von Protokollen und Metriken an Event Hubs ermöglicht es Ihnen, Daten an externe Systeme wie SIEMs von Drittanbietern und andere Log Analytics-Lösungen zu streamen.  |
| [Azure Monitor – integrierte Partnerlösungen](/azure/partner-solutions/overview/)| Spezialisierte Integrationen zwischen Azure Monitor und anderen Überwachungsplattformen, die nicht von Mircrosoft stammen. Nützlich, wenn Sie bereits einen der Partner nutzen.  |

## <a name="requirements-and-limitations"></a>Anforderungen und Einschränkungen

### <a name="metrics-as-a-source"></a>Metriken als Quelle
Der Export von Metriken unterliegt gewissen Einschränkungen.

- **Das Senden von mehrdimensionalen Metriken über Diagnoseeinstellungen wird derzeit nicht unterstützt** - Metriken mit Dimensionen werden als abgeflachte eindimensionale Metriken exportiert, die über Dimensionswerte aggregiert werden. *Beispiel*: Die Metrik „IOReadBytes“ in einer Blockchain kann für jeden Knoten einzeln untersucht und dargestellt werden. Beim Exportieren über die Diagnoseeinstellungen zeigt die exportierte Metrik jedoch alle gelesenen Bytes für alle Knoten an.
- **Nicht alle Metriken sind mit Diagnoseeinstellungen exportierbar** - Aufgrund interner Beschränkungen sind nicht alle Metriken in Azure Monitor Logs / Log Analytics exportierbar. Weitere Informationen finden Sie in der Spalte exportierbar in der [Liste der unterstützten Metriken](./metrics-supported.md)

Um diese Einschränkungen für bestimmte Metriken zu umgehen, können Sie diese manuell mit der [Metrics REST API](/rest/api/monitor/metrics/list) extrahieren und mit der [Azure Monitor Data collector API](../logs/data-collector-api.md) in Azure Monitor Logs importieren.

### <a name="activity-log-as-a-source"></a>Aktivitätsprotokoll als Quelle

> [!IMPORTANT]
> Bevor Sie eine Diagnoseeinstellung für das Aktivitätsprotokoll erstellen, sollten Sie zunächst vorhandene Legacykonfigurationen deaktivieren. Weitere Informationen hierzu finden Sie unter [Legacy-Erfassungsmethoden](../essentials/activity-log.md#legacy-collection-methods).


### <a name="destination-limitations"></a>Grenzen des Reiseziels

Alle Ziele für Diagnoseeinstellungen müssen vor den Diagnoseeinstellungen erstellt werden. Das Ziel muss sich nicht in demselben Abonnement befinden wie die Ressource, die Protokolle sendet, sofern der Benutzer, der die Einstellung konfiguriert, den entsprechenden Azure RBAC-Zugriff auf beide Abonnements besitzt. Mit Azure Lighthouse ist es auch möglich, Diagnoseeinstellungen an einen Arbeitsbereich in einem anderen Mandanten in Azure Active Directory senden zu lassen. In der folgenden Tabelle sind besondere Anforderungen für die einzelnen Ziele einschließlich regionaler Einschränkungen aufgeführt.

| Destination | Requirements (Anforderungen) |
|:---|:---|
| Log Analytics-Arbeitsbereich | Der Arbeitsbereich muss sich nicht in derselben Region wie die überwachte Ressource befinden.|
| Azure-Speicherkonto | Verwenden Sie kein bestehendes Speicherkonto, in dem andere, nicht überwachungsrelevante Daten gespeichert sind, damit Sie den Zugriff auf die Daten besser kontrollieren können. Wenn Sie das Aktivitätsprotokoll und Ressourcenprotokollen zusammen archivieren, können Sie aber dasselbe Speicherkonto verwenden, damit sich alle Überwachungsdaten an einem zentralen Ort befinden.<br><br>Legen Sie die unveränderliche Richtlinie für das Speicherkonto wie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](../../storage/blobs/immutable-policy-configure-version-scope.md) beschrieben fest, um die Daten an einen unveränderlichen Speicher zu senden. Sie müssen alle Schritte in diesem verlinkten Artikel befolgen, einschließlich der Aktivierung des geschützten Schreibens von Append Blobs.<br><br>Wenn die überwachte Ressource regional ist, muss sich das Speicherkonto in derselben Region wie die Ressource befinden.|
| Event Hubs | Die SAS-Richtlinie für den Namespace definiert die Berechtigungen für den Streamingmechanismus. Für das Streaming an Event Hubs werden Berechtigungen für das Verwalten, Senden und Lauschen benötigt. Wenn Sie der Diagnoseeinstellung das Streamingfeature hinzufügen möchten, müssen Sie in der Event Hubs-Autorisierungsregel über die ListKey-Berechtigung verfügen.<br><br>Wenn die überwachte Ressource regional ist, muss sich der Event Hub-Namespace in derselben Region wie die Ressource befinden. <br><br> Diagnoseeinstellungen können nicht auf Event-Hubs-Ressourcen zugreifen, wenn virtuelle Netzwerke aktiviert sind. Sie müssen die Einstellung *Vertrauenswürdige Microsoft-Dienste zulassen*, um diese Firewall in Event Hub zu umgehen, aktivieren, damit der Dienst Azure Monitor (Diagnoseeinstellungen) Zugriff auf Ihre Event Hub-Ressourcen erhält.|
| Partnerintegrationen | Variiert je nach Partner.  Weitere Informationen finden Sie in der [Azure Monitor-Partnerintegrationsdokumentation](/azure/partner-solutions/overview/).  

### <a name="azure-data-lake-storage-gen2-as-a-destination"></a>Azure Data Lake Storage Gen2 als Ziel

> [!NOTE]
> Azure Data Lake Storage Gen2-Konten werden derzeit nicht als Ziel für Diagnoseeinstellungen unterstützt, auch wenn sie möglicherweise als gültige Option im Azure-Portal aufgeführt werden.

## <a name="create-in-azure-portal"></a>Im Azure-Portal erstellen

Sie können Diagnoseeinstellungen im-Azure-Portal entweder über das Azure Monitor-Menü oder über das Menü für die Ressource konfigurieren.

1. Wo Sie Diagnoseeinstellungen im Azure-Portal konfigurieren, hängt von der Ressource ab.

   - Für eine einzelne Ressource klicken Sie im Ressourcenmenü unter **Überwachen** auf **Diagnoseeinstellungen**.

        ![Screenshot des Bereichs „Überwachung“ eines Ressourcenmenüs im Azure-Portal mit hervorgehobener Option „Diagnoseeinstellungen“](media/diagnostic-settings/menu-resource.png)

   - Für eine oder mehrere Ressourcen klicken Sie im Azure Monitor-Menü unter **Einstellungen** auf **Diagnoseeinstellungen**, und klicken Sie dann auf die Ressource.

        ![Screenshot des Abschnitts „Einstellungen“ im Azure Monitor-Menü mit hervorgehobener Option „Diagnoseeinstellungen“](media/diagnostic-settings/menu-monitor.png)

   - Für das Aktivitätsprotokoll klicken Sie im **Azure Monitor**-Menü auf **Aktivitätsprotokoll** und dann auf **Diagnoseeinstellungen**. Stellen Sie sicher, dass Sie alle Legacykonfigurationen für das Aktivitätsprotokoll deaktivieren. Ausführliche Informationen dazu finden Sie unter [Deaktivieren vorhandener Einstellungen](./activity-log.md#legacy-collection-methods).

        ![Screenshot des Azure Monitor-Menüs mit ausgewähltem Aktivitätsprotokoll und hervorgehobener Option „Diagnoseeinstellungen“ in der Menüleiste „Aktivitätsprotokoll“ in Azure Monitor](media/diagnostic-settings/menu-activity-log.png)

2. Wenn keine Einstellungen für die Ressource vorhanden sind, die Sie ausgewählt haben, werden Sie aufgefordert, eine Einstellung zu erstellen. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   ![Diagnoseeinstellung hinzufügen – keine Einstellungen vorhanden](media/diagnostic-settings/add-setting.png)

   Wenn bereits Einstellungen für die Ressource vorhanden sind, wird eine Liste der konfigurierten Einstellungen anagezeigt. Klicken Sie entweder auf **Diagnoseeinstellung hinzufügen**, um eine neue Einstellung hinzuzufügen, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten. Jede Einstellung kann höchstens einen der Zieltypen aufweisen.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/diagnostic-settings/edit-setting.png)

3. Wenn noch kein Name für die Einstellung vorhanden ist, geben Sie ihr einen Namen.

      :::image type="Add diagnostic setting" source="media/diagnostic-settings/setting-new-blank.png" alt-text="Eine neue Diagnoseeinstellung hinzufügen":::

4. **Zu routende Protokolle und Metriken:** Wählen Sie für Protokolle entweder eine Kategoriegruppe aus, oder aktivieren Sie die einzelnen Kontrollkästchen für jede Kategorie von Daten, die Sie an die später angegebenen Ziele senden möchten. Die Liste der Kategorien unterscheidet sich je nach Azure-Dienst. Choose *allMetrics* if you want to store metrics into Azure Monitor Logs as well. 

5. **Zieldetails:** Aktivieren Sie das Kontrollkästchen für jedes Ziel. Beim Aktivieren der einzelnen Kontrollkästchen werden Optionen angezeigt, mit denen Sie zusätzliche Informationen hinzufügen können.

      ![Senden an Log Analytics oder Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics:** Geben Sie das Abonnement und den Arbeitsbereich ein.  Wenn Sie noch nicht über einen Arbeitsbereich verfügen, [erstellen Sie vor dem Fortfahren einen](../logs/quick-create-workspace.md).

    1. **Ereignis-Hubs** - Geben Sie die folgenden Kriterien an:
       - Das Abonnement, zu dem der Event Hub gehört
       - Der Event Hub-Namespace: Wenn Sie noch keinen besitzen, müssen Sie [einen erstellen](../../event-hubs/event-hubs-create.md).
       - Ein Event Hub-Name (optional), an den alle Daten gesendet werden sollen. Wenn Sie keinen Namen angeben, wird eine Event Hub für jede Protokollkategorie erstellt. Wenn Sie mehrere Kategorien senden, können Sie einen Namen angeben, um die Anzahl der erstellten Event Hubs zu beschränken. Ausführliche Informationen dazu finden Sie unter [Kontingente und Grenzwerte in Azure Event Hubs](../../event-hubs/event-hubs-quotas.md).
       - Eine Event Hub-Richtlinie (optional): definiert die Berechtigungen für den Streamingmechanismus. Weitere Informationen finden Sie unter [Event Hubs-Features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Speicher:** Wählen Sie das Abonnement, das Speicherkonto und die Aufbewahrungsrichtlinie aus.

        ![Senden an den Speicher](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Denken Sie daran, die Aufbewahrungsrichtlinie auf 0 festzulegen, und verwenden Sie entweder die [ Lebenszyklusrichtlinie für den Azure-Speicher](/azure/storage/blobs/lifecycle-management-policy-configure) oder löschen Sie Ihre Daten mithilfe eines geplanten Auftrags aus dem Speicher. Diese Vorgehensweise sorgt wahrscheinlich für ein konstanteres Verhalten. 
        >
        > Erstens: Wenn Sie Speicher für die Archivierung verwenden, sollten Sie die Daten in der Regel für mehr als 365 Tage aufbewahren. Zweitens: Wenn Sie eine Aufbewahrungsrichtlinie über 0 auswählen, wird das Ablaufdatum beim Zeitpunkt des Speicherns an die Protokolle angefügt. Nach dem Speichern können Sie das Datum für diese Protokolle nicht mehr ändern. Wenn Sie z. B. die Aufbewahrungsrichtlinie für *WorkflowRuntime* auf 180 Tage und dann 24 Stunden später auf 365 Tage festlegen, werden die in den ersten 24 Stunden gespeicherten Protokolle automatisch nach 180 Tagen gelöscht, während alle nachfolgenden Protokolle dieses Typs nach 365 Tagen automatisch gelöscht werden. Das Ändern der Aufbewahrungsrichtlinie zu einem späteren Zeitpunkt sorgt nicht dafür, dass die Protokolle aus den ersten 24 Stunden 365 Tage lang aufbewahrt werden.

     1. **Partnerintegration** - Sie müssen zunächst eine Partnerintegration in Ihrem Abonnement installieren. Die Konfigurationsoptionen variieren je nach Partner. Weitere Informationen finden Sie unter [Azure Monitor-Partnerintegrationen](/azure/partner-solutions/overview/). 
    
6. Klicken Sie auf **Speichern**.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Ressource angezeigt, und Protokolle werden an die angegebenen Ziele gestreamt, sobald neue Ereignisdaten generiert werden. Zwischen der Ausgabe eines Ereignisses und dessen [Anzeige in einem Log Analytics-Arbeitsbereich](../logs/data-ingestion-time.md) können bis zu 15 Minuten vergehen.

## <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

Verwenden Sie das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting), um eine Diagnoseeinstellung mit [Azure PowerShell](../powershell-samples.md) zu erstellen. Beschreibungen der zugehörigen Parameter finden Sie in der Dokumentation zu diesem Cmdlet.

> [!IMPORTANT]
> Sie können diese Methode nicht für das Azure-Aktivitätsprotokoll verwenden. Gehen Sie stattdessen nach der Methode [Erstellen von Diagnoseeinstellungen in Azure Monitor mithilfe einer Resource Manager-Vorlage](./resource-manager-diagnostic-settings.md) vor, um eine Resource Manager-Vorlage zu erstellen und mit PowerShell bereitzustellen.

Es folgt ein Beispiel für ein PowerShell-Cmdlet zum Erstellen einer Diagnoseeinstellung, die alle drei Ziele verwendet.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Erstellen mithilfe der Azure CLI

Verwenden Sie den Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create), um eine Diagnoseeinstellung mit der [Azure CLI](/cli/azure/monitor) zu erstellen. Beschreibungen der zugehörigen Parameter finden Sie in der Dokumentation zu diesem Befehl.

> [!IMPORTANT]
> Sie können diese Methode nicht für das Azure-Aktivitätsprotokoll verwenden. Gehen Sie stattdessen nach der Methode [Erstellen von Diagnoseeinstellungen in Azure Monitor mithilfe einer Resource Manager-Vorlage](./resource-manager-diagnostic-settings.md) vor, um eine Resource Manager-Vorlage zu erstellen und mit der CLI bereitzustellen.

Es folgt ein Beispiel für einen CLI-Befehl zum Erstellen einer Diagnoseeinstellung, die alle drei Ziele verwendet. Die Syntax kann je nach Client geringfügig variieren.

# <a name="cmd"></a>[Befehlszeile](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Erstellen mithilfe einer Resource Manager-Vorlage
Informationen zum Erstellen oder Aktualisieren von Diagnoseeinstellungen mithilfe einer Resource Manager-Vorlage finden Sie unter [Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen in Azure Monitor](./resource-manager-diagnostic-settings.md).

## <a name="create-using-rest-api"></a>Erstellen mithilfe der REST-API
Informationen zum Erstellen oder Aktualisieren von Diagnoseeinstellungen mithilfe der [Azure Monitor-REST-API](/rest/api/monitor/) finden Sie unter [Diagnoseeinstellungen](/rest/api/monitor/diagnosticsettings).

## <a name="create-at-scale-using-azure-policy"></a>Erstellen im großen Stil mit Azure Policy

Da für jede Azure-Ressource eine Diagnoseeinstellung erstellt werden muss, können Sie mit Azure Policy beim Erstellen der einzelnen Ressourcen automatisch eine Diagnoseeinstellung erstellen. Jeder Azure-Ressourcentyp verfügt über einen eindeutigen Satz von Kategorien, die in der Diagnoseeinstellung aufgelistet werden müssen. Aus diesem Grund ist für jeden Ressourcentyp eine eigene Richtliniendefinition erforderlich. Einige Ressourcentypen verfügen über integrierte Richtliniendefinitionen, die Sie ohne Änderungen zuweisen können. Bei anderen Ressourcentypen müssen Sie eine benutzerdefinierte Definition erstellen. 

Mit der Hinzufügung von Gruppen für Ressourcenprotokollkategorien können Sie jetzt Optionen wählen, die dynamisch aktualisiert werden, wenn sich die Protokollkategorien ändern.  Weitere Informationen finden Sie in den Quellen für [Diagnoseeinstellungen](#sources), die weiter oben in diesem Artikel aufgeführt sind. 

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Integrierte Richtliniendefinitionen für Azure Monitor
Es gibt zwei integrierte Richtliniendefinitionen für jeden Ressourcentyp, eine zum Senden an den Log Analytics-Arbeitsbereich und eine andere zum Senden an einen Event Hub. Wenn Sie nur einen Speicherort benötigen, weisen Sie diese Richtlinie für den Ressourcentyp zu. Wenn Sie beide benötigen, weisen Sie beiden Richtliniendefinitionen für die Ressource zu.

Die folgende Abbildung zeigt z. B. die integrierten Richtliniendefinitionen für die Diagnoseeinstellung für Azure Data Lake Analytics.

![Teilscreenshot der Seite „Definitionen“ von Azure Policy mit zwei integrierten Richtliniendefinitionen für die Diagnoseeinstellung für Data Lake Analytics](media/diagnostic-settings/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Benutzerdefinierte Richtliniendefinitionen
Für Ressourcentypen, die über keine integrierte Richtlinie verfügen, müssen Sie eine benutzerdefinierte Richtliniendefinition erstellen. Sie können hierzu manuell im Azure-Portal eine vorhandene integrierte Richtlinie kopieren und dann für Ihren Ressourcentyp ändern. Es ist jedoch effizienter, die Richtlinie programmgesteuert mithilfe eines Skripts im PowerShell-Katalog zu erstellen.

Das Skript [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) erstellt Richtliniendateien für einen bestimmten Ressourcentyp, die Sie mithilfe von PowerShell oder der Azure CLI installieren können. Erstellen Sie mit folgendem Verfahren eine benutzerdefinierte Richtliniendefinition für Diagnoseeinstellungen:

1. Vergewissern Sie sich, dass [Azure PowerShell](/powershell/azure/install-az-ps) installiert ist.
2. Installieren Sie das Skript mit dem folgenden Befehl:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Führen Sie das Skript mit den Parametern aus, die angeben, wohin die Protokolle gesendet werden sollen. Sie werden aufgefordert, ein Abonnement und einen Ressourcentyp anzugeben. 

   Wenn Sie z. B. eine Richtliniendefinition erstellen möchten, die Protkolle an einen Log Analytics-Arbeitsbereich und einen Event Hub sendet, verwenden Sie den folgenden Befehl:

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

   Alternativ können Sie ein Abonnement und einen Ressourcentyp im Befehl angeben. Wenn Sie z. B. eine Richtliniendefinition erstellen möchten, die an einen Log Analytics-Arbeitsbereich und einen Event Hub für Azure SQL Server-Datenbanken sendet, verwenden Sie den folgenden Befehl:

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Das Skript erstellt separate Ordner für jede Richtliniendefinition. Jeder Ordner enthält drei Dateien mit den Namen *azurepolicy.json*, *azurepolicy.rules.json* und *azurepolicy.parameters.json*. Wenn Sie die Richtlinie manuell im Azure-Portal erstellen möchten, können Sie den Inhalt von *azurepolicy.json* kopieren und einfügen, da diese Datei die gesamte Richtliniendefinition enthält. Verwenden Sie die anderen beiden Dateien mit PowerShell oder der Azure CLI, um die Richtliniendefinition über eine Befehlszeile zu erstellen.

   In den folgenden Beispielen wird gezeigt, wie die Richtliniendefinition sowohl über PowerShell als auch über die Azure CLI installiert wird. Beide Beispiele enthalten Metadaten zum Angeben einer Kategorie zur **Überwachung**, um die neue Richtliniendefinition mit den integrierten Richtliniendefinitionen zu gruppieren.

   ```azurepowershell
   New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
   ```

   ```azurecli
   az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
   ```

### <a name="initiative"></a>Initiative
Anstatt für jede Richtliniendefinition eine Zuweisung zu erstellen, ist es eine gängige Strategie, eine Initiative zu erstellen, die die Richtliniendefinitionen umfasst, um Diagnoseeinstellungen für jeden Azure-Dienst zu erstellen. Erstellen Sie abhängig von der Art der Verwaltung Ihrer Umgebung eine Zuweisung zwischen der Initiative und einer Verwaltungsgruppe, einem Abonnement oder einer Ressourcengruppe. Diese Strategie bietet folgende Vorteile:

- Erstellen Sie eine einzelne Zuweisung für die Initiative anstelle mehrerer Zuweisungen für jeden Ressourcentyp. Verwenden Sie dieselbe Initiative für mehrere Überwachungsgruppen, Abonnements oder Ressourcengruppen.
- Ändern Sie die Initiative, wenn Sie einen neuen Ressourcentyp oder ein neues Ziel hinzufügen müssen. Beispielsweise können die ersten Anforderungen darin bestehen, Daten nur an einen Log Analytics-Arbeitsbereich zu senden, aber später möchten Sie einen Event Hub hinzufügen. Ändern Sie die Initiative, anstatt neue Zuweisungen zu erstellen.

Ausführliche Informationen zum Erstellen einer Initiative finden Sie unter [Erstellen und Zuweisen einer Initiativdefinition](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition). Beachten Sie die folgenden Empfehlungen:

- Legen Sie **Kategorie** auf **Überwachung** fest, um sie mit zugehörigen integrierten und benutzerdefinierten Richtliniendefinitionen zu gruppieren.
- Anstatt die Details für den Log Analytics-Arbeitsbereich und den Event Hub für die Richtliniendefinition in der Initiative anzugeben, verwenden Sie einen allgemeinen Initiativparameter. Mit diesem Parameter können Sie auf einfache Weise einen allgemeinen Wert für alle Richtliniendefinitionen angeben und diesen Wert bei Bedarf ändern.

![Screenshot: Einstellungen für die Initiativdefinition](media/diagnostic-settings/initiative-definition.png)

### <a name="assignment"></a>Zuweisung 
Weisen Sie die Initiative abhängig vom Umfang ihrer zu überwachenden Ressourcen einer Azure-Verwaltungsgruppe, einem Abonnement oder einer Ressourcengruppe zu. Eine [Verwaltungsgruppe](../../governance/management-groups/overview.md) ist nützlich für bereichsbezogene Richtlinien, insbesondere wenn Ihre Organisation über mehrere Abonnements verfügt.

![Screenshot: Einstellungen auf der Registerkarte „Grundlagen“ im Abschnitt „Initiative zuweisen“ der Diagnoseeinstellungen für den Log Analytics-Arbeitsbereich im Azure-Portal](media/diagnostic-settings/initiative-assignment.png)

Mithilfe von Initiativparametern können Sie den Arbeitsbereich oder andere Details einmal für alle Richtliniendefinitionen in der Initiative angeben. 

![Screenshot: Initiativenparameter auf der Registerkarte „Parameter“](media/diagnostic-settings/initiative-parameters.png)

### <a name="remediation"></a>Wiederherstellung
Die Initiative gilt für jeden virtuellen Computer, während er erstellt wird. Mit einem [Wartungstask](../../governance/policy/how-to/remediate-resources.md) werden die Richtliniendefinitionen in der Initiative für vorhandene Ressourcen bereitgestellt, sodass Sie Diagnoseeinstellungen für alle Ressourcen erstellen können, die bereits erstellt wurden.

Wenn Sie die Zuweisung mithilfe des Azure-Portals erstellen, können Sie gleichzeitig einen Wartungstask erstellen. Weitere Informationen zur Wartung finden Sie unter [Korrigieren nicht konformer Ressourcen mit Azure Policy](../../governance/policy/how-to/remediate-resources.md).

![Screenshot: Korrektur eines Log Analytics-Arbeitsbereichs im Rahmen einer Initiative](media/diagnostic-settings/initiative-remediation.png)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="metric-category-is-not-supported"></a>Nicht unterstützte Metrikkategorie

Beim Bereitstellen einer Diagnoseeinstellung erhalten Sie eine Fehlermeldung ähnlich wie *Metrikkategorie „xxxx“ wird nicht unterstützt*. Dieser Fehler wird möglicherweise angezeigt, auch wenn ihre vorige Bereitstellung erfolgreich war. 

Das Problem tritt auf, wenn eine Resource Manager-Vorlage, die REST-API für Diagnoseeinstellungen, Azure CLI oder Azure PowerShell verwendet wird. Über das Azure-Portal erstellte Diagnoseeinstellungen sind nicht betroffen, da nur die unterstützten Kategorienamen angezeigt werden.

Das Problem wird von einer vor Kurzem erfolgten Änderung an der zugrunde liegenden API verursacht. Andere Metrikkategorien als "AllMetrics" werden nicht unterstützt und wurden mit Ausnahme einiger weniger spezifischer Azure-Dienste auch nie unterstützt. In der Vergangenheit wurden andere Kategorienamen beim Bereitstellen einer Diagnoseeinstellung ignoriert. Das Azure Monitor-Backend leitete diese Kategorien auf "AllMetrics" um.  Im Februar 2021 wurde das Back-End aktualisiert, sodass speziell dafür gesorgt ist, dass die bereitgestellte Metrikkategorie korrekt ist. Diese Änderungen führt zu Fehlern bei einigen Bereitstellungen.

Wenn Sie diesen Fehler erhalten, aktualisieren Sie Ihre Bereitstellungen, sodass alle Metrikkategorienamen durch 'AllMetrics' ersetzt werden, um das Problem zu beheben. Wenn bei der Bereitstellung zuvor mehrere Kategorien hinzugefügt wurden, sollten Sie nur den 'AllMetrics'-Verweis beibehalten. Wenn das Problem weiterhin besteht, wenden Sie sich an den Azure-Support über das Azure-Portal. 

### <a name="setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>Einstellung wird aufgrund von Nicht-ASCII-Zeichen in resourceID nicht mehr angezeigt

Diagnoseeinstellungen unterstützen keine resourceIDs mit Nicht-ASCII-Zeichen (z. B. „Präproduktion“). Da Sie Ressourcen in Azure nicht umbenennen können, besteht Ihre einzige Möglichkeit darin, eine neue Ressource ohne die Nicht-ASCII-Zeichen zu erstellen. Wenn sich die Zeichen in einer Ressourcengruppe befinden, können Sie die darin enthaltenen Ressourcen in eine neue verschieben. Andernfalls müssen Sie die Ressource neu erstellen.

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu Protokollen der Azure-Plattform](./platform-logs-overview.md)
