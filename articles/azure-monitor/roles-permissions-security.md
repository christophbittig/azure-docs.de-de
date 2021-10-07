---
title: Rollen, Berechtigungen und Sicherheit in Azure Monitor
description: Erfahren Sie mehr über die Verwendung der Rollen und Berechtigungen in Azure Monitor, um den Zugriff auf Überwachungsressourcen zu beschränken.
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b4335f3a6690ef502bdd495ea0de317eacfe738
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788684"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Rollen, Berechtigungen und Sicherheit in Azure Monitor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Viele Teams müssen den Zugriff auf Überwachungsdaten und -einstellungen streng regulieren. Wenn einige Ihrer Teammitglieder beispielsweise ausschließlich an der Überwachung arbeiten (Supporttechniker, DevOps-Techniker) oder wenn Sie einen verwalteten Dienstanbieter verwenden, sollten Sie diesen nur Zugriff auf Überwachungsdaten erteilen. Möglicherweise sollten Sie die Möglichkeit einschränken, Ressourcen zu erstellen, zu ändern oder zu löschen. 

Dieser Artikel beschreibt, wie Sie schnell eine integrierte Rolle zur Überwachung auf einen Benutzer in Azure anwenden oder Ihre eigene benutzerdefinierte Rolle für einen Benutzer erstellen, der eingeschränkte Überwachungsberechtigungen benötigt. Anschließend werden im Artikel Sicherheitsaspekte für Ihre Azure Monitor-Ressourcen erörtert, und es wird beschrieben, wie Sie den Zugriff auf die Daten in diesen Ressourcen beschränken können.

## <a name="built-in-monitoring-roles"></a>Integrierte Überwachungsrollen
Integrierte Rollen in Azure Monitor helfen dabei, den Zugriff auf Ressourcen in einem Abonnement einzuschränken, während das Personal für die Infrastrukturüberwachung weiterhin die benötigten Daten abrufen und konfigurieren kann. Azure Monitor bietet zwei vorkonfigurierte Rollen: Überwachungsleser und Überwachungsmitwirkender.

### <a name="monitoring-reader"></a>Überwachungsleser
Personen, denen die Überwachungsleserrolle zugewiesen wird, können alle Überwachungsdaten in einem Abonnement anzeigen, aber keine Ressourcen ändern oder Einstellungen im Zusammenhang mit der Ressourcenüberwachung bearbeiten. Diese Rolle eignet sich für Benutzer in einer Organisation, beispielsweise Support- oder Betriebstechniker, die folgende Aufgaben erfüllen müssen:

* Anzeigen von Überwachungsdashboards im Azure-Portal
* Anzeigen von in [Azure-Warnungen](alerts/alerts-overview.md) definierten Warnungsregeln
* Abfragen von Metriken über die [Azure Monitor-REST-API](/rest/api/monitor/metrics), [PowerShell-Cmdlets](powershell-samples.md) oder die [plattformübergreifende Befehlszeilenschnittstelle](cli-samples.md)
* Abfragen des Aktivitätsprotokolls über das Portal, die Azure Monitor-REST-API, PowerShell-Cmdlets oder die plattformübergreifende Befehlszeilenschnittstelle
* Anzeigen der [Diagnoseeinstellungen](essentials/diagnostic-settings.md) für eine Ressource.
* Anzeigen des [Protokollprofils](essentials/activity-log.md#legacy-collection-methods) für ein Abonnement.
* Anzeigen von Einstellungen für die automatische Skalierung.
* Anzeigen von Warnaktivitäten und -einstellungen.
* Zugreifen auf Application Insights-Daten und Anzeigen von Daten in Application Insights Analytics
* Durchsuchen von Arbeitsbereichsdaten von Log Analytics, einschließlich Nutzungsdaten für den Arbeitsbereich
* Anzeigen von Verwaltungsgruppen in Log Analytics
* Abrufen des Suchschemas in einem Log Analytics-Arbeitsbereich
* Auflisten von Überwachungspaketen in einem Log Analytics-Arbeitsbereich
* Abrufen und Ausführen gespeicherter Suchvorgänge in einem Log Analytics-Arbeitsbereich
* Abrufen der Speicherkonfiguration des Arbeitsbereichs für Log Analytics

> [!NOTE]
> Diese Rolle erteilt keinen Lesezugriff für Protokolldaten, die an einen Event Hub gestreamt oder in einem Speicherkonto gespeichert wurden. Informationen zum Konfigurieren des Zugriffs auf diese Ressourcen finden Sie im Abschnitt [Sicherheitsaspekte für Überwachungsdaten](#security-considerations-for-monitoring-data) weiter unten in diesem Artikel. 

### <a name="monitoring-contributor"></a>Überwachungsmitwirkender
Personen, denen die Rolle „Überwachungsmitwirkender“ zugewiesen wurde, können alle Überwachungsdaten in einem Abonnement anzeigen. Sie können auch Überwachungseinstellungen erstellen oder ändern, aber keine anderen Ressourcen ändern. 

Diese Rolle ist der Rolle „Überwachungsleser“ übergeordnet. Sie ist für Mitglieder des Überwachungsteams einer Organisation oder für verwaltete Dienstanbieter geeignet, die zusätzlich zu den oben genannten Berechtigungen zu Folgendem in der Lage sein müssen:

* Anzeigen von Überwachungsdashboards im Portal und Erstellen ihrer eigenen privaten Überwachungsdashboards.
* Festlegen von [Diagnoseeinstellungen](essentials/diagnostic-settings.md) für eine Ressource.\*
* Festlegen des [Protokollprofils](essentials/activity-log.md#legacy-collection-methods) für ein Abonnement.\*
* Festlegen der Aktivität und der Einstellungen für Warnungsregeln über [Azure-Warnungen](alerts/alerts-overview.md).
* Erstellen von Webtests und Komponenten für Application Insights.
* Auflisten der freigegebenen Schlüssel für einen Log Analytics-Arbeitsbereich.
* Aktivieren oder Deaktivieren von Überwachungspaketen in einem Log Analytics-Arbeitsbereich.
* Erstellen, Löschen und Ausführen gespeicherter Suchvorgänge in einem Log Analytics-Arbeitsbereich.
* Erstellen und Löschen der Speicherkonfiguration des Arbeitsbereichs für Log Analytics.

\*Dem Benutzer muss außerdem gesondert die ListKeys-Berechtigung für die Zielressource (Speicherkonto oder Event Hub-Namespace) erteilt werden, um ein Protokollprofil oder eine Diagnoseeinstellung festzulegen.

> [!NOTE]
> Diese Rolle erteilt keinen Lesezugriff für Protokolldaten, die an einen Event Hub gestreamt oder in einem Speicherkonto gespeichert wurden. Informationen zum Konfigurieren des Zugriffs auf diese Ressourcen finden Sie im Abschnitt [Sicherheitsaspekte für Überwachungsdaten](#security-considerations-for-monitoring-data) weiter unten in diesem Artikel. 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Überwachen von Berechtigungen und benutzerdefinierte Azure-Rollen
Wenn die vorhergehenden vordefinierten Rollen nicht den genauen Anforderungen Ihres Teams entsprechen, können Sie eine [benutzerdefinierte Azure-Rolle](../role-based-access-control/custom-roles.md) mit detaillierteren Berechtigungen erstellen. Hier folgen die allgemeinen Vorgänge der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure für Azure Monitor:

| Vorgang | BESCHREIBUNG |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Lesen, Schreiben oder Löschen einer Aktionsgruppe |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Aktivitätsprotokollwarnungen |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Warnungsregeln (aus klassischen Warnungen) |
| Microsoft.Insights/AlertRules/Incidents/Read |Auflisten von Incidents (Verlauf der ausgelösten Warnungsregel) für Warnungsregeln. Dies gilt nur für das Portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Einstellungen für die automatische Skalierung |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Diagnoseeinstellungen |
| Microsoft.Insights/EventCategories/Read |Aufzählen aller im Aktivitätsprotokoll möglichen Kategorien Wird vom Azure-Portal verwendet. |
| Microsoft.Insights/eventtypes/digestevents/Read |Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf das Aktivitätsprotokoll zugreifen müssen. |
| Microsoft.Insights/eventtypes/values/Read |Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Lesen, Schreiben oder Löschen von Diagnoseeinstellungen für Netzwerkflussprotokolle |
| Microsoft.Insights/LogDefinitions/Read |Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf das Aktivitätsprotokoll zugreifen müssen. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Protokollprofilen (Streamen des Aktivitätsprotokolls an ein Ereignishub- oder Speicherkonto) |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Metrikwarnungen (in Quasi-Echtzeit) |
| Microsoft.Insights/MetricDefinitions/Read |Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
| Microsoft.Insights/Metrics/Read |Lesen von Metriken für eine Ressource. |
| Microsoft.Insights/Register/Action |Registrieren des Azure Monitor-Ressourcenanbieters |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Lesen, Schreiben oder Löschen von Protokollwarnungen in Azure Monitor |

> [!NOTE]
> Für den Zugriff auf Warnungen, Diagnoseeinstellungen und Metriken für eine Ressource ist es erforderlich, dass der Benutzer für den Ressourcentyp und den Bereich der jeweiligen Ressource über Lesezugriff verfügt. Für das Erstellen (Schreiben) einer Diagnoseeinstellung oder eines Protokollprofils, bei der bzw. dem Daten in einem Speicherkonto archiviert oder an Event Hubs gestreamt werden, muss der Benutzer zudem die ListKeys-Berechtigung für die Zielressource besitzen. 

Beispielsweise können Sie die obige Tabelle verwenden, um wie folgt eine benutzerdefinierte Azure-Rolle für einen Aktivitätsprotokollleser zu erstellen:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Sicherheitsaspekte für Überwachungsdaten
Überwachungsdaten – besonders Protokolldateien – können vertrauliche Informationen wie IP-Adressen oder Benutzernamen enthalten. Überwachungsdaten aus Azure werden in drei grundlegenden Varianten bereitgestellt:

- Das Aktivitätsprotokoll, in dem alle Aktionen auf Steuerungsebene in Ihrem Azure-Abonnement beschrieben werden.
- Diagnoseprotokolle, die von einer Ressource ausgegeben werden.
- Metriken, die von Ressourcen ausgegeben werden.

Sämtliche dieser Datentypen können in einem Speicherkonto gespeichert oder an Event Hub gestreamt werden. Bei beidem handelt es sich um allgemeine Azure-Ressourcen. Da es sich um allgemeine Ressourcen handelt, sind Erstellung, Löschung und der Zugriff darauf privilegierte Vorgänge, die einem Administrator vorbehalten sind. Wir empfehlen, die folgenden Methoden für überwachungsbezogene Ressourcen anzuwenden, um Missbrauch zu verhindern:

* Verwenden Sie ein einzelnes dediziertes Speicherkonto für die Überwachung von Daten. Wenn Sie Überwachungsdaten auf mehrere Speicherkonten aufteilen müssen, sollten Sie die Nutzung eines Speicherkontos niemals zwischen Überwachungsdaten und Nicht-Überwachungsdaten aufteilen. Eine solche gemeinsame Nutzung könnte Organisationen, die nur Zugriff auf Überwachungsdaten benötigen, unbeabsichtigt Zugriff auf Nicht-Überwachungsdaten verschaffen. Beispielsweise sollte eine Drittanbieterorganisation für SIEM (Security Information & Event Management) nur Zugriff auf Überwachungsdaten benötigen.
* Verwenden Sie aus demselben Grund, der unter dem vorherigen Punkt beschrieben wurde, einen einzelnen dedizierten Service Bus- oder Event Hub-Namespace über alle Diagnoseeinstellungen hinweg.
* Beschränken Sie den Zugriff auf überwachungsbezogene Speicherkonten oder Event Hubs, indem Sie sie in einer separaten Ressourcengruppe aufbewahren. [Verwenden Sie den Gültigkeitsbereich](../role-based-access-control/overview.md#scope) für Ihre Überwachungsrollen, um den Zugriff ausschließlich auf diese Ressourcengruppe zu beschränken.
* Erteilen Sie niemals die ListKeys-Berechtigung für Speicherkonten oder Event Hubs im Abonnementbereich, wenn ein Benutzer nur auf Überwachungsdaten zugreifen muss. Erteilen Sie dem Benutzer diese Berechtigungen stattdessen im Ressourcen- oder Ressourcengruppenbereich (wenn Sie eine dedizierte Überwachungsressourcengruppe verwenden).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Einschränken des Zugriffs auf überwachungsbezogene Speicherkonten
Wenn ein Benutzer oder eine Anwendung Zugriff auf Überwachungsdaten in einem Speicherkonto benötigt, sollten Sie für das Speicherkonto, das Überwachungsdaten enthält, [eine Shared Access Signature (SAS)](/rest/api/storageservices/create-account-sas) mit Nur-Lese-Zugriff auf Dienstebene für Blobspeicher generieren. In PowerShell könnte die Konto-SAS wie der folgende Code aussehen:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Sie können dann das Token an die Entität weitergeben, die aus diesem Speicherkonto lesen muss. Die Entität kann alle Blobs in diesem Speicherkonto auflisten und aus diesen lesen.

Wenn Sie diese Berechtigung mit Azure RBAC steuern müssen, können Sie dieser Entität alternativ die Berechtigung `Microsoft.Storage/storageAccounts/listkeys/action` für das jeweilige Speicherkonto erteilen. Diese Berechtigung ist für Benutzer notwendig, die eine Diagnoseeinstellung oder ein Protokollprofil für die Archivierung in einem Speicherkonto festlegen müssen. Beispielsweise können Sie die folgende benutzerdefinierte Azure-Rolle für Benutzer oder Anwendungen erstellen, die nur aus einem einzigen Speicherkonto lesen müssen:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Die ListKeys-Berechtigung ermöglicht dem Benutzer das Auflisten des primären und des sekundären Speicherkontoschlüssels. Diese Schlüssel gewähren dem Benutzer alle signierten Berechtigungen (z. B. Lesen, Schreiben, Erstellen von Blobs und Löschen von Blobs) für alle signierten Dienste (Blob, Warteschlange, Tabelle, Datei) in diesem Speicherkonto. Wir empfehlen, wenn möglich eine Konto-SAS zu verwenden. 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Einschränken des Zugriffs auf überwachungsbezogene Event Hubs
Sie können ein ähnliches Muster mit Event Hubs befolgen, aber zuerst müssen Sie eine dedizierte Autorisierungsregel für das Lauschen erstellen. Wenn Sie einer Anwendung Zugriff erteilen möchten, die nur auf überwachungsbezogene Event Hubs lauschen muss, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Portal eine SAS-Richtlinie für die Event Hubs, die für das Streamen von Überwachungsdaten mit ausschließlich Lauschansprüchen erstellt haben. Beispielsweise könnten Sie die Richtlinie „monitoringReadOnly“ nennen. Wenn möglich, übergeben Sie diesen Schlüssel direkt an den Consumer, und überspringen Sie den nächsten Schritt.
2. Wenn der Consumer den Schlüssel ad hoc abrufen können muss, gewähren Sie dem Benutzer die ListKeys-Aktion für diesen Event Hub. Dieser Schritt ist auch für Benutzer notwendig, die eine Diagnoseeinstellung oder ein Protokollprofil für das Streamen in Event Hubs festlegen müssen. Beispielsweise können Sie eine Azure RBAC-Regel erstellen:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.EventHub/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.EventHub/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Überwachung innerhalb eines gesicherten virtuellen Netzwerks

Azure Monitor benötigt Zugriff auf Ihre Azure-Ressourcen, um die von Ihnen aktivierten Dienste bereitzustellen. Wenn Sie Ihre Azure-Ressourcen überwachen und gleichzeitig vor dem Zugriff auf das öffentliche Internet schützen möchten, können Sie gesicherte Speicherkonten verwenden.

Überwachungsdaten werden häufig in ein Speicherkonto geschrieben. Vielleicht möchten Sie sicherstellen, dass unbefugte Benutzer keinen Zugriff auf die Daten haben, die in ein Speicherkonto kopiert wurden. Für zusätzliche Sicherheit können Sie den Netzwerkzugriff sperren, um nur Ihren autorisierten Ressourcen und vertrauenswürdigen Microsoft-Diensten den Zugriff auf ein Speicherkonto zu ermöglichen, indem Sie ein Speicherkonto auf die Verwendung durch ausgewählte Netzwerke beschränken.

![Screenshot: Einstellungen für Firewalls und virtuelle Netzwerke](./media/roles-permissions-security/secured-storage-example.png)

Azure Monitor gilt als vertrauenswürdiger Microsoft-Dienst. Wenn Sie das Kontrollkästchen **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktivieren, hat Azure Monitor Zugriff auf Ihr gesichertes Speicherkonto. Sie aktivieren dann das Schreiben von Azure Monitor-Ressourcenprotokollen, Aktivitätsprotokollen und Metriken in Ihr Speicherkonto unter diesen geschützten Bedingungen. Durch diese Einstellung kann Log Analytics auch Protokolle aus dem geschützten Speicher lesen.   

Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Nächste Schritte
* [Weitere Informationen zu Azure RBAC und Berechtigungen in Azure Resource Manager](../role-based-access-control/overview.md)
* [Übersicht über die Überwachung in Microsoft Azure](overview.md)

