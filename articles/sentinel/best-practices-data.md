---
title: Bewährte Methoden für die Datensammlung in Microsoft Sentinel
description: Erfahren Sie mehr über bewährte Methoden beim Verbinden von Datenquellen mit Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 17561127c040fc1aedac771093e0bfa6366c4d30
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524776"
---
#  <a name="data-collection-best-practices"></a>Bewährte Methoden für die Datensammlung

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Abschnitt werden bewährte Methoden zum Sammeln von Daten mithilfe von Microsoft Sentinel-Daten-Connectors erläutert. Weitere Informationen finden Sie unter [Verbinden von Datenquellen](connect-data-sources.md), im [Verweis auf Microsoft Sentinel-Daten-Connectors](data-connectors-reference.md) sowie im [Microsoft Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).

## <a name="prioritize-your-data-connectors"></a>Priorisieren Ihrer Datenconnectors

Wenn nicht klar ist, welche Datenconnectors am besten für Ihre Umgebung geeignet sind, aktivieren Sie zunächst alle [kostenlosen Datenconnectors](azure-sentinel-billing.md#free-data-sources).

Die kostenlosen Daten-Connectors zeigen so bald wie möglich einen Wert aus Microsoft Sentinel an, während Sie weiterhin andere Daten-Connectors und Budgets planen.

Für Ihre [Partner](data-connectors-reference.md)- und [benutzerdefinierten](create-custom-connector.md) Datenconnectors legen Sie zunächst die [Syslog](connect-syslog.md)- und [CEF](connect-common-event-format.md)-Connectors mit der höchsten Priorität fest, ebenso wie alle Linux-basierten Geräte.

Wenn Ihre Datenerfassung zu schnell zu teuer wird, beenden oder filtern Sie die mit dem [Azure Monitor-Agent](../azure-monitor/agents/azure-monitor-agent-overview.md) weitergeleiteten Protokolle.

> [!TIP]
> Mit benutzerdefinierten Daten-Connectors können Sie Daten in Microsoft Sentinel aus Datenquellen erfassen, die derzeit nicht von integrierten Funktionen wie Agent, Logstash oder API unterstützt werden. Weitere Informationen finden Sie unter [Ressourcen zum Erstellen benutzerdefinierter Microsoft Sentinel-Connectors](create-custom-connector.md).
>

## <a name="filter-your-logs-before-ingestion"></a>Filtern von Protokollen vor der Erfassung

Möglicherweise möchten Sie die gesammelten Protokolle oder sogar den Protokollinhalt filtern, bevor die Daten in Microsoft Sentinel erfasst werden. Beispielsweise können Sie Protokolle herausfiltern, die für Sicherheitsvorgänge irrelevant oder unwichtig sind, oder unerwünschte Details aus Protokollmeldungen entfernen. Das Filtern von Nachrichteninhalten kann auch hilfreich sein, wenn Sie versuchen, die Kosten bei der Arbeit mit Syslog, CEF oder Windows-basierten Protokollen zu senken, die viele irrelevante Details enthalten.

Filtern Sie Ihre Protokolle mit einer der folgenden Methoden:

- **Azure Monitor-Agent**. Wird sowohl unter Windows als auch unter Linux unterstützt, um [Windows Sicherheitsereignisse](connect-windows-security-events.md) zu erfassen. Filtern Sie die gesammelten Protokolle, indem Sie den Agent so konfigurieren, dass nur angegebene Ereignisse erfasst werden.

- **Logstash**. Unterstützt das Filtern von Nachrichteninhalten, einschließlich Änderungen an den Protokollmeldungen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Logstash](create-custom-connector.md#connect-with-logstash).

> [!IMPORTANT]
> Die Verwendung von Logstash zum Filtern Ihres Nachrichteninhalts führt dazu, dass Ihre Protokolle als benutzerdefinierte Protokolle erfasst werden, sodass Protokolle im [Free-Tarif](azure-sentinel-billing.md#free-data-sources) zu Protokollen eines kostenpflichtigen Tarifs werden.
>
> Benutzerdefinierte Protokolle müssen auch in [Analyseregeln](automate-incident-handling-with-automation-rules.md), [Bedrohungssuche](hunting.md) und [Arbeitsmappen](get-visibility.md) eingearbeitet werden, da sie nicht automatisch hinzugefügt werden. Benutzerdefinierte Protokolle werden derzeit auch nicht für Funktionen des [maschinellen Lernens](bring-your-own-ml.md) unterstützt.
>

## <a name="alternative-data-ingestion-requirements"></a>Alternative Datenerfassungsanforderungen

Die Standardkonfiguration für die Datensammlung funktioniert aufgrund verschiedener Herausforderungen möglicherweise nicht gut für Ihre Organisation. In den folgenden Tabellen werden allgemeine Herausforderungen oder Anforderungen sowie mögliche Lösungen und Überlegungen beschrieben.

> [!NOTE]
> Viele der unten aufgeführten Lösungen erfordern einen benutzerdefinierten Datenconnector. Weitere Informationen finden Sie unter [Ressourcen zum Erstellen benutzerdefinierter Microsoft Sentinel-Connectors](create-custom-connector.md).
>

### <a name="on-premises-windows-log-collection"></a>Lokale Windows-Protokollsammlung


|Herausforderung/Anforderung  |Lösungsvorschläge  |Weitere Überlegungen  |
|---------|---------|---------|
|**Erfordert Protokollfilterung**     | Verwenden von Logstash <br><br>Verwenden von Azure Functions <br><br> Verwenden von LogicApps <br><br> Verwenden von benutzerdefiniertem Code (.NET, Python)  |  Während die Filterung zu Kosteneinsparungen führen kann und nur die benötigten Daten erfasst, werden einige Microsoft Sentinel-Funktionen nicht unterstützt, darunter [UEBA](identify-threats-with-entity-behavior-analytics.md), [Entitätsseiten](identify-threats-with-entity-behavior-analytics.md#entity-pages), [maschinelles Lernen](bring-your-own-ml.md) und [Fusion](fusion.md). <br><br>Beim Konfigurieren der Protokollfilterung müssen Sie Aktualisierungen in Ressourcen vornehmen, z. B. in Abfragen zur Bedrohungssuche und Analyseregeln.     |
|**Agent kann nicht installiert werden**     |Verwenden von Windows-Ereignisweiterleitung, unterstützt mit dem [Azure Monitor-Agent](connect-windows-security-events.md#connector-options)       |   Die Verwendung von Windows-Ereignisweiterleitung senkt die Lastausgleichsereignisse der Windows-Ereignissammlung von 10.000 Ereignissen auf 500 bis 1000 Ereignisse pro Sekunde.|
|**Server stellen keine Verbindung mit dem Internet her**     | Verwenden des [Log Analytics-Gateways](../azure-monitor/agents/gateway.md)        | Zum Konfigurieren eines Proxys für Ihren Agent sind zusätzliche Firewallregeln erforderlich, damit das Gateway funktioniert.        |
|**Erfordert Tagging und Anreicherung bei der Erfassung**     |Verwenden von Logstash zum Einfügen einer ResourceID <br><br>Verwenden einer ARM-Vorlage zum Einfügen der ResourceID in lokale Computer <br><br>Erfassen der Ressourcen-ID in separaten Arbeitsbereichen        | Log Analytics unterstützt keine RBAC für benutzerdefinierte Tabellen <br><br>Microsoft Sentinel unterstützt keine RBAC auf Zeilenebene <br><br>**Tipp**: Möglicherweise möchten Sie Design und Funktionen für Microsoft Sentinel verwenden, die arbeitsbereichsübergreifend sind.        |
|**Erfordert das Aufteilen von Vorgangs- und Sicherheitsprotokollen**     | Verwenden der Multi-Home-Funktionalität von [Microsoft Monitor-Agent oder Azure Monitor-Agent](connect-windows-security-events.md)        |  Multi-Home-Funktionalität erfordert mehr Bereitstellungsaufwand für den Agent.       |
|**Erfordert benutzerdefinierte Protokolle**     |   Sammeln von Dateien aus bestimmten Ordnerpfaden <br><br>Verwenden von API-Erfassung <br><br>Verwenden von PowerShell <br><br>Verwenden von Logstash     |   Möglicherweise haben Sie Probleme beim Filtern Ihrer Protokolle. <br><br>Benutzerdefinierte Methoden werden nicht unterstützt. <br><br>Benutzerdefinierte Connectors erfordern möglicherweise Entwicklerkenntnisse.       |
| | | |

### <a name="on-premises-linux-log-collection"></a>Lokale Linux-Protokollsammlung

|Herausforderung/Anforderung  |Lösungsvorschläge  |Weitere Überlegungen  |
|---------|---------|---------|
|**Erfordert Protokollfilterung**     | Verwenden von Syslog-NG <br><br>Verwenden von Rsyslog <br><br>Verwenden von FluentD-Konfiguration für den Agent <br><br> Verwenden des Azure Monitor-Agents/Microsoft Monitoring Agent <br><br> Verwenden von Logstash  |  Einige Linux-Distributionen werden vom Agent möglicherweise nicht unterstützt. <br> <br>Für die Verwendung von Syslog oder FluentD sind Entwicklerkenntnisse erforderlich. <br><br>Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Windows-Servern, um sicherheitsrelevantes Ereignisse zu sammeln](connect-windows-security-events.md) und [Ressourcen zum Erstellen von benutzerdefinierten Microsoft Sentinel-Connectors](create-custom-connector.md).      |
|**Agent kann nicht installiert werden**     |  Verwenden Sie eine Syslog-Weiterleitung, z. B. syslog-ng oder rsyslog.       |         |
|**Server stellen keine Verbindung mit dem Internet her**       | Verwenden des [Log Analytics-Gateways](../azure-monitor/agents/gateway.md)        | Zum Konfigurieren eines Proxys für Ihren Agent sind zusätzliche Firewallregeln erforderlich, damit das Gateway funktioniert.          |
|**Erfordert Tagging und Anreicherung bei der Erfassung**      | Verwenden Sie Logstash für Anreicherung oder benutzerdefinierte Methoden wie API oder EventHubs.         | Möglicherweise ist für die Filterung zusätzlicher Aufwand erforderlich.    |
|**Erfordert das Aufteilen von Vorgangs- und Sicherheitsprotokollen**     |  Verwenden Sie den [Azure Monitor-Agent](connect-windows-security-events.md) mit der Multi-Homing-Konfiguration.       |         |
|**Erfordert benutzerdefinierte Protokolle**     |    Erstellen Sie einen benutzerdefinierten Collector mit dem Microsoft Monitoring-Agent (Log Analytics).      |      |
| | | |

### <a name="endpoint-solutions"></a>Endpunktlösungen

Wenn Sie Protokolle von Endpunktlösungen wie EDR, anderen Sicherheitsereignissen, Sysmon usw. sammeln müssen, verwenden Sie eine der folgenden Methoden:

- **MTP-Connector** zum Sammeln von Protokollen von Microsoft 365 Defender für Endpunkt. Diese Option verursacht zusätzliche Kosten für die Datenerfassung.
- **Windows-Ereignisweiterleitung**.

> [!NOTE]
> Der Lastenausgleich reduziert die Ereignisse pro Sekunde, die im Arbeitsbereich verarbeitet werden können.
>

### <a name="office-data"></a>Office-Daten

Wenn Sie Microsoft Office Daten außerhalb der Standardconnectordaten sammeln müssen, verwenden Sie eine der folgenden Lösungen:

|Herausforderung/Anforderung  |Lösungsvorschläge  |Weitere Überlegungen  |
|---------|---------|---------|
|**Sammeln von Rohdaten aus Teams, Nachrichtenablaufverfolgung, Phishingdaten usw.**     |    Verwenden Sie die integrierten Funktionen des [Office 365-Connectors](./data-connectors-reference.md#microsoft-office-365), und erstellen Sie dann einen benutzerdefinierten Connector für andere Rohdaten.  |  Das Zuordnen von Ereignissen zur entsprechenden recordID kann eine Herausforderung darstellen.  |
|**Erfordert RBAC zum Aufteilen von Ländern, Abteilungen usw.**     | Passen Sie Ihre Datensammlung an, indem Sie Daten Tags hinzufügen und dedizierte Arbeitsbereiche für jede benötigte Trennung erstellen.|   Die benutzerdefinierte Datensammlung verursacht zusätzliche Erfassungskosten.     |
|**Erfordert mehrere Mandanten in einem einzelnen Arbeitsbereich**     |  Passen Sie Ihre Datensammlung mithilfe von Azure LightHouse und einer einheitlichen Incidentansicht an.|  Die benutzerdefinierte Datensammlung verursacht zusätzliche Erfassungskosten.  <br><br>Weitere Informationen finden Sie unter [Erweitern von Microsoft Sentinel auf Arbeitsbereiche und Mandanten](extend-sentinel-across-workspaces-tenants.md).      |
| | | |

### <a name="cloud-platform-data"></a>Cloudplattformdaten

|Herausforderung/Anforderung  |Lösungsvorschläge  |Weitere Überlegungen  |
|---------|---------|---------|
|**Filtern von Protokollen von anderen Plattformen**     | Verwenden von Logstash <br><br>Verwenden des Azure Monitor-Agents/Microsoft Monitoring Agent (Log Analytics)       |    Die benutzerdefinierte Sammlung verursacht zusätzliche Erfassungskosten. <br><br>Es kann eine Herausforderung sein, alle Windows-Ereignisse und nicht nur Sicherheitsereignisse zu sammeln.     |
|**Agent kann nicht verwendet werden**     |   Verwenden von Windows-Ereignisweiterleitung      | Möglicherweise müssen Sie Lastausgleich für Ihre Ressourcen vornehmen.        |
|**Server befinden sich im Air-Gap-Netzwerk**     | Verwenden des [Log Analytics-Gateways](../azure-monitor/agents/gateway.md)        | Zum Konfigurieren eines Proxys für Ihren Agent sind Firewallregeln erforderlich, damit das Gateway funktioniert.        |
|**RBAC, Tagging und Anreicherung bei der Erfassung**     |    Erstellen Sie eine benutzerdefinierte Sammlung über Logstash oder die Log Analytics-API.     |  RBAC wird für benutzerdefinierte Tabellen nicht unterstützt <br><br>RBAC auf Zeilenebene wird für Tabellen nicht unterstützt.       |
|     |         |         |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Aktivitäten vor der Bereitstellung und Voraussetzungen für die Bereitstellung von Microsoft Sentinel](prerequisites.md)
- [Bewährte Methoden für Microsoft Sentinel](best-practices.md)
- [Herstellen einer Verbindung mit Datenquellen](connect-data-sources.md)
