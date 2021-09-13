---
title: Übersicht über die Benachrichtigung und Benachrichtigungsüberwachung in Azure
description: Übersicht über Warnungen in Azure Monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ee0d6cd4c895bbcd78767776a86bd63cfa4f5242
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340105"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Überblick über Warnungen in Microsoft Azure 

In diesem Artikel wird beschrieben, was Warnungen sind, welche Vorteile sie haben und wie Sie mit der Nutzung beginnen.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Was sind Warnungen in Microsoft Azure?

Warnungen benachrichtigen Sie proaktiv unter Verwendung der Überwachungsdaten in Azure Monitor, wenn Probleme mit Ihrer Infrastruktur oder Anwendung auftreten. Sie ermöglichen es Ihnen, Probleme zu identifizieren und zu beheben, bevor die Benutzer Ihres Systems sie bemerken. 

## <a name="overview"></a>Übersicht

Das folgende Diagramm zeigt den Warnungsablauf. 

![Diagramm des Warnungsablaufs](media/alerts-overview/Azure-Monitor-Alerts.svg)

Die Warnungsregeln sind von den Warnungen und Aktionen getrennt, die beim Auslösen einer Warnung ergriffen werden. Die Warnungsregel erfasst das Ziel und die Kriterien für Warnungen. Die Warnungsregel kann sich im Zustand „Aktiviert“ oder „Deaktiviert“ befinden. Warnungen werden nur ausgelöst, wenn sie aktiviert sind. 

Nachfolgend sind die wichtigste Attribute einer Warnungsregel aufgeführt:

**Zielressource**: Die Zielressource definiert den Umfang und die für Warnungen verfügbaren Signale. Ein Ziel kann eine beliebige Azure-Ressource sein. Beispielziele:

- Virtuelle Computer
- Speicherkonten
- Log Analytics-Arbeitsbereich.
- Application Insights. 

Für bestimmte Ressourcen (wie virtuelle Computer) können Sie mehrere Ressourcen als Ziel der Warnungsregel angeben.

**Signal**: Wird von der Zielressource ausgegeben. Zulässige Signaltypen sind: Metrik, Aktivitätsprotokoll, Application Insights und Protokoll.

**Kriterien**: Eine Kombination aus Signal und Logik, die auf eine Zielressource angewendet wird. Beispiele: 

- CPU in Prozent > 70 %
- Serverantwortzeit > 4 ms 
- Anzahl der Ergebnisse einer Protokollabfrage > 100

**Warnungsname**: Ein bestimmter Name für die Warnungsregel, der vom Benutzer konfiguriert wird

**Warnungsbeschreibung**: Eine Beschreibung für die Warnungsregel, die vom Benutzer konfiguriert wird.

**Schweregrad**: Der Schweregrad der Warnung, nachdem die in der Warnungsregel angegebene Kriterien erfüllt sind. Der Schweregrad kann zwischen 0 und 4 liegen.

- Schweregrad 0 = Kritischer Fehler
- Schweregrad 1 = Fehler
- Schweregrad 2 = Warnung
- Schweregrad 3 = Information
- Schweregrad 4 = Ausführlicher Modus 

**Aktion**: Eine bestimmte Aktion, die ausgeführt wird, sobald die Warnung ausgelöst wird. Weitere Informationen finden Sie unter [Aktionsgruppen](../alerts/action-groups.md).

## <a name="what-you-can-alert-on"></a>Wofür Sie Warnungen ausgeben können

Sie können Warnungen für Metriken und Protokolle ausgeben, wie es unter [Überwachen von Datenquellen](./../agents/data-sources.md) beschrieben ist. Signale umfassen u. a. Folgendes:

- Metrikwerte
- Protokollsuchabfragen
- Aktivitätsprotokollereignisse
- Integrität der zugrunde liegenden Azure-Plattform
- Tests für die Verfügbarkeit von Websites

## <a name="manage-alerts"></a>Warnungen verwalten

Sie können den Status einer Warnung festlegen, um anzugeben, an welchem Punkt des Lösungsprozesses sie sich befindet. Wenn die in der Warnungsregel angegebenen Kriterien erfüllt sind, wird eine Warnung erstellt oder ausgelöst, die den Status *Neu* aufweist. Sie können den Status ändern, wenn Sie eine Warnung bestätigen oder schließen. Alle Statusänderungen werden im Warnungsverlauf gespeichert.

Die folgenden Warnungsstatus werden unterstützt.

| State | BESCHREIBUNG |
|:---|:---|
| Neu | Das Problem wurde erkannt und noch nicht überprüft. |
| Bestätigt | Ein Administrator hat die Warnung überprüft und mit deren Bearbeitung begonnen. |
| Geschlossen | Das Problem wurde gelöst. Nachdem eine Warnung geschlossen wurde, können Sie diese erneut öffnen, indem Sie den Status ändern. |

*Warnungsstatus*: Der Warnungsstatus ist unabhängig von der *Überwachungsbedingung* und unterscheidet sich von dieser. Der Warnungsstatus wird vom Benutzer festgelegt. Die Überwachungsbedingung wird vom System bestimmt. Wenn eine Warnung ausgelöst wird, wird die Überwachungsbedingung der Warnung auf *Ausgelöst* festgelegt, und wenn die zugrunde liegende Bedingung gelöscht wurde, die die Warnung ausgelöst hat, wird die Überwachungsbedingung auf *Behoben* festgelegt. 

Der Warnungsstatus wird erst geändert, wenn der Benutzer ihn geändert hat. Erfahren Sie mehr über das [Ändern des Zustands Ihrer Warnungen und intelligenten Gruppen](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

## <a name="alerts-experience"></a>Benutzeroberfläche „Warnungen“ 
Die Standardseite „Warnungen“ enthält eine Zusammenfassung der Warnungen, die in einem bestimmten Zeitbereich erstellt werden. Sie zeigt die gesamten Warnungen für jeden Schweregrad mit Spalten an, in denen die Gesamtanzahl der Warnungen im jeweiligen Status für jeden Schweregrad angegeben ist. Klicken Sie auf einen der Schweregrade, um die Seite [Alle Warnungen](#all-alerts-page) gefiltert nach diesem Schweregrad zu öffnen.

Stattdessen können Sie [die Warnungsinstanzen, die für Ihre Abonnements mit REST-APIs generiert werden, programmgesteuert auflisten](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Sie können nur auf Warnungen zugreifen, die in den letzten 30 Tagen generiert wurden.

Sie können die Abonnements oder Filterparameter ändern, um die Seite zu aktualisieren.

![Screenshot der Seite „Warnungen“](media/alerts-overview/alerts-page.png)

Zum Filtern dieser Ansicht können Sie Werte in den Dropdownmenüs am oberen Rand der Seite auswählen.

| Column | BESCHREIBUNG |
|:---|:---|
| Subscription | Wählen Sie die Azure-Abonnements aus, für die Sie Warnungen anzeigen möchten. Sie können auch alle Ihre Abonnements auswählen. Nur Warnungen, auf die Sie Zugriff in den ausgewählten Abonnements haben, sind in der Ansicht enthalten. |
| Resource group | Wählen Sie eine einzelne Ressourcengruppe aus. Nur Warnungen mit Zielen in der ausgewählten Ressourcengruppe sind in der Ansicht enthalten. |
| Uhrzeitbereich | Nur Warnungen, die innerhalb des ausgewählten Zeitbereichs ausgelöst wurden, sind in der Ansicht enthalten. Unterstützte Werte sind die letzte Stunde, die letzten 24 Stunden, die letzten 7 Tage und die letzten 30 Tage. |

Klicken Sie auf die folgenden Werte am oberen Rand der Seite „Warnungen“, um eine andere Seite zu öffnen:

| Wert | BESCHREIBUNG |
|:---|:---|
| Warnungen gesamt | Die Gesamtanzahl der Warnungen, die den ausgewählten Kriterien entsprechen. Wählen Sie diesen Wert aus, um die Ansicht „Alle Warnungen“ ohne Filter zu öffnen. |
| Intelligente Gruppen | Die Gesamtanzahl anhand von Warnungen erstellter intelligenter Gruppen, die den ausgewählten Kriterien entsprechen. Wählen Sie diesen Wert aus, um die Liste „Intelligente Gruppen“ in der Ansicht „Alle Warnungen“ zu öffnen.
| Warnungsregeln gesamt | Die Gesamtanzahl der Warnungsregeln im ausgewählten Abonnement und der ausgewählten Ressourcengruppe. Wählen Sie diesen Wert aus, um die Ansicht „Regeln“ gefiltert nach ausgewählten Abonnements und ausgewählter Ressourcengruppe zu öffnen.


## <a name="manage-alert-rules"></a>Verwalten von Warnungsregeln
Klicken Sie auf **Warnungsregeln verwalten**, um die Seite **Regeln** anzuzeigen. Diese Seite ist ein zentraler Ort, an dem Sie alle Warnungsregeln in Ihren Azure-Abonnements verwalten können. Sie listet alle Warnungsregeln auf und kann basierend auf Zielressourcen, Ressourcengruppen, Regelnamen oder Status sortiert werden. Auf dieser Seite können Sie auch Warnungsregeln bearbeiten, aktivieren oder deaktivieren.  

 ![Screenshot der Seite „Regeln“](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel
Sie können Warnungsregeln unabhängig vom Überwachungsdienst oder Signaltyp auf konsistente Weise erstellen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Gehen Sie zum Erstellen einer neuen Warnungsregel wie folgt vor:
1. Wählen Sie das _Ziel_ für die Warnung aus.
1. Wählen Sie das _Signal_ aus den verfügbaren Signalen für das Ziel aus.
1. Geben Sie die _Logik_ an, die auf Daten vom Signal angewendet werden soll.

Bei dieser vereinfachten Erstellung müssen Sie nicht mehr die Überwachungsquelle oder -signale kennen, die unterstützt werden, bevor eine Azure-Ressource ausgewählt wird. Die Liste verfügbarer Signale wird automatisch auf Grundlage der ausgewählten Zielressource gefiltert. Basierend auf diesem Ziel werden Sie auch automatisch durch das Festlegen der Logik der Warnungsregel geführt.  

Weitere Informationen zum Erstellen von Warnungsregeln finden Sie unter [Erstellen, Anzeigen und Verwalten von Warnungen mithilfe von Azure Monitor](../alerts/alerts-metric.md).

Warnungen sind übergreifend für mehrere Azure-Überwachungsdienste verfügbar. Informationen dazu, wie und wann Sie die jeweiligen Dienste verwenden, finden Sie unter [Überwachen von Azure-Anwendungen und -Ressourcen](../overview.md). 


## <a name="all-alerts-page"></a>Seite „Alle Warnungen“ 
Klicken Sie auf **Warnungen gesamt**, um die Seite **Alle Warnungen** anzuzeigen. Hier können Sie eine Liste der Warnungen anzeigen, die innerhalb des ausgewählten Zeitraums erstellt wurden. Sie können entweder eine Liste der einzelnen Warnungen oder eine Liste der intelligenten Gruppen anzeigen, die diese Warnungen enthalten. Klicken Sie auf das Banner am oberen Rand der Seite, um zwischen den Ansichten zu wechseln.

![Screenshot der Seite „Alle Warnungen“](media/alerts-overview/all-alerts-page.png)

Zum Filtern der Ansicht können Sie die folgenden Werte in den Dropdownmenüs am oberen Rand der Seite auswählen:

| Column | BESCHREIBUNG |
|:---|:---|
| Subscription | Wählen Sie die Azure-Abonnements aus, für die Sie Warnungen anzeigen möchten. Sie können auch alle Ihre Abonnements auswählen. Nur Warnungen, auf die Sie Zugriff in den ausgewählten Abonnements haben, sind in der Ansicht enthalten. |
| Resource group | Wählen Sie eine einzelne Ressourcengruppe aus. Nur Warnungen mit Zielen in der ausgewählten Ressourcengruppe sind in der Ansicht enthalten. |
| Ressourcentyp | Wählen Sie mindestens einen Ressourcentyp aus. Nur Warnungen mit Zielen des ausgewählten Typs sind in der Ansicht enthalten. Diese Spalte ist nur verfügbar, nachdem eine Ressourcengruppe angegeben wurde. |
| Resource | Wählen Sie eine Ressource aus. Nur Warnungen mit dieser Ressource als Ziel sind in der Ansicht enthalten. Diese Spalte ist nur verfügbar, nachdem ein Ressourcentyp angegeben wurde. |
| severity | Wählen Sie einen Warnungsschweregrad oder **Alle** aus, um Warnungen aller Schweregrade einzuschließen. |
| Überwachungsbedingung | Wählen Sie eine Überwachungsbedingung oder **Alle** aus, um Warnungen aller Bedingungen einzuschließen. |
| Warnungsstatus | Wählen Sie einen Warnungsstatus oder **Alle** aus, um Warnungen aller Statusarten einzuschließen. |
| Überwachungsdienst | Wählen Sie einen Dienst oder **Alle** aus, um alle Dienste einzuschließen. Nur Warnungen, die anhand von Regeln erstellt wurden, die diesen Dienst als Ziel verwenden, sind enthalten. |
| Uhrzeitbereich | Nur Warnungen, die innerhalb des ausgewählten Zeitbereichs ausgelöst wurden, sind in der Ansicht enthalten. Unterstützte Werte sind die letzte Stunde, die letzten 24 Stunden, die letzten 7 Tage und die letzten 30 Tage. |

Klicken Sie am oberen Rand der Seite auf **Spalten**, um die anzuzeigenden Spalten auszuwählen. 

## <a name="alert-details-page"></a>Seite mit Warnungsdetails
Wenn Sie eine Warnung auswählen, zeigt diese Seite Details der Warnung und ermöglicht Ihnen das Ändern des Status.

![Screenshot der Seite „Warnungsdetails“](media/alerts-overview/alert-detail2.png)

Die Seite „Warnungsdetails“ enthält die folgenden Abschnitte:

| `Section` | BESCHREIBUNG |
|:---|:---|
| Zusammenfassung | Zeigt die Eigenschaften und andere wichtige Informationen zur Warnung an. |
| Verlauf | Listet die einzelnen Aktionen, die von der Warnung ausgeführt wurden, und alle an der Warnung vorgenommenen Änderungen auf. Das beschränkt sich derzeit auf Statusänderungen. |
| Diagnose | Informationen zur intelligenten Gruppe, der die Warnung angehört. Die *Warnungsanzahl* bezieht sich auf die Anzahl der Warnungen, die in der intelligenten Gruppe enthalten sind. Die Informationen enthalten andere Warnungen in der gleichen intelligenten Gruppe, die in den letzten 30 Tagen erstellt wurden, unabhängig vom Zeitfilter auf der Seite mit der Warnungsliste. Klicken Sie auf eine Warnung, um deren Details anzuzeigen. |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für Ihre Warnungsinstanzen

Für die Nutzung und Verwaltung von Warnungsinstanzen muss der Benutzer über die integrierten Azure-Rollen [Mitwirkender an der Überwachung](../../role-based-access-control/built-in-roles.md#monitoring-contributor) oder [Benutzer mit Leseberechtigung für Überwachungsdaten](../../role-based-access-control/built-in-roles.md#monitoring-reader) verfügen. Diese Rollen werden in jedem Azure Resource Manager-Umfang unterstützt, unabhängig davon, ob es sich um die Abonnementebene oder um genau abgestimmte Zuweisungen auf Ressourcenebene handelt. Wenn ein Benutzer z.B. nur den Zugriff „Mitwirkender an der Überwachung“ für den virtuellen Computer `ContosoVM1` hat, kann dieser Benutzer nur Warnungen, die auf `ContosoVM1` erstellt wurden, erhalten und verwalten.

## <a name="manage-your-alert-instances-programmatically"></a>Programmgesteuertes Verwalten Ihrer Warnungsinstanzen

Möglicherweise möchten Sie Warnungen, die für Ihr Abonnement generiert wurden, programmgesteuert abfragen. Beispielsweise können Sie mit Abfragen benutzerdefinierte Ansichten außerhalb des Azure-Portals erstellen oder Ihre Warnungen analysieren, um Muster und Trends zu erkennen.

Es empfiehlt sich, [Azure Resource Graph](../../governance/resource-graph/overview.md) mit dem `AlertsManagementResources`-Schema zum Abfragen von ausgelösten Warnungen zu verwenden. Resource Graph wird empfohlen, wenn Sie Warnungen verwalten müssen, die für mehrere Abonnements generiert werden.

Die folgende Beispielanforderung an die Resource Graph-REST-API gibt die Warnungen des letzten Tages in einem Abonnement zurück:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "alertsmanagementresources | where properties.essentials.lastModifiedDateTime > ago(1d) | project alertInstanceId = id, parentRuleId = tolower(tostring(properties['essentials']['alertRule'])), sourceId = properties['essentials']['sourceCreatedId'], alertName = name, severity = properties.essentials.severity, status = properties.essentials.monitorCondition, state = properties.essentials.alertState, affectedResource = properties.essentials.targetResourceName, monitorService = properties.essentials.monitorService, signalType = properties.essentials.signalType, firedTime = properties['essentials']['startDateTime'], lastModifiedDate = properties.essentials.lastModifiedDateTime, lastModifiedBy = properties.essentials.lastModifiedUserName"
}
```

Sie können das Ergebnis dieser Resource Graph-Abfrage im Portal mit dem Azure Resource Graph-Explorer anzeigen: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/alertsmanagementresources%0A%7C%20where%20properties.essentials.lastModifiedDateTime%20%3E%20ago(1d)%0A%7C%20project%20alertInstanceId%20%3D%20id%2C%20parentRuleId%20%3D%20tolower(tostring(properties%5B 'essentials'%5D%5B'alertRule'%5D))%2C%20sourceId%20%3D%20properties%5B'essentials'%5D%5B'sourceCreatedId'%5D%2C%20alertName%20%3D%20name%2C%20severity%20%3D%20properties.essentials.severity%2C%20status%20%3D%20properties.essentials.monitorCondition%2C%20state%20%3D%20properties.essentials.alertState%2C%20affectedResource%20%3D%20properties.essentials.targetResourceName%2C%20monitorService%20%3D%20properties.essentials.monitorService%2C%20signalType%20%3D%20properties.essentials.signalType%2C%20firedTime%20%3D%20properties%5B'essentials'%5D%5B'startDateTime'%5D%2C%20lastModifiedDate%20%3D%20properties.essentials.lastModifiedDateTime%2C%20lastModifiedBy%20%3D%20properties.essentials.lastModifiedUserName)

Sie können die [Warnungsverwaltungs-REST-API](/rest/api/monitor/alertsmanagement/alerts) auch in kleineren Abfrageszenarios verwenden oder zum Aktualisieren von ausgelösten Warnungen nutzen.

## <a name="smart-groups"></a>Intelligente Gruppen

Intelligente Gruppen sind Aggregationen von Warnungen basierend auf Algorithmen des maschinellen Lernens, die dabei helfen, Störungen bei Warnungen zu verringern sowie Probleme zu beheben. [Erfahren Sie mehr über intelligente Gruppen](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) und [zum Verwalten Ihrer intelligenten Gruppen](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu intelligenten Gruppen](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Informationen zu Aktionsgruppen](../alerts/action-groups.md)
- [Verwalten Ihrer Warnungsinstanzen in Azure](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Verwalten intelligenter Gruppen](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Weitere Informationen zu Azure-Warnungspreisen](https://azure.microsoft.com/pricing/details/monitor/)
