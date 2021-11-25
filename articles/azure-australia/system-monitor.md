---
title: Systemüberwachung aus Sicherheitsgründen für Azure Australien
description: Leitfaden zum Konfigurieren der Systemüberwachung in den australischen Regionen, um die spezifischen Anforderungen der Richtlinien, Bestimmungen und Gesetze der australischen Behörden zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 8c67cc2ff2d918b8d44d8a362b3b6c1609f17f13
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312850"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Systemüberwachung aus Sicherheitsgründen für Azure Australien

Die Verwendung robuster Sicherheitsstrategien mit Echtzeitüberwachung und routinemäßigen Sicherheitsbewertungen sind für Sie von entscheidender Bedeutung, um die allgemeine Betriebssicherheit für Ihre IT-Umgebungen, einschließlich der Cloud, zu verbessern.

Das Erzielen von Cloudsicherheit ist gemeinsame Aufgabe des Kunden und des Cloudanbieters. Microsoft Azure verfügt über vier Dienste, mit denen diese Anforderungen gemäß den Empfehlungen im [Handbuch zur Informationssicherheit (Information Security Manual, ISM) des Australian Cyber Security Centre (ACSC) zum Thema Kontrollmaßnahmen](https://acsc.gov.au/infosec/ism/index.htm) erfüllt werden können. Hierbei geht es vor allem um die Implementierung der zentralisierten Ereignisprotokollierung, die Überprüfung von Ereignisprotokollen und die Bewertung und das Management von Sicherheitsrisiken. Diese Microsoft Azure-Dienste lauten:

* Microsoft Defender für Cloud
* Azure Monitor
* Azure Advisor
* Azure Policy

Vom ACSC wird empfohlen, diese Dienste für Daten vom Typ **PROTECTED** (GESCHÜTZT) zu verwenden. Bei Verwendung dieser Dienste können Sie Ihre IT-Umgebungen proaktiv überwachen und analysieren und fundierte Entscheidungen zur Zuordnung von Ressourcen treffen, um die Sicherheit zu erhöhen. Jeder dieser Dienste ist Teil einer kombinierten Lösung, um für Sie in Bezug auf Erkenntnisse, Empfehlungen und den Schutz das bestmögliche Ergebnis zu erzielen.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender für Cloud

[Microsoft Defender für Cloud](../security-center/security-center-introduction.md) verfügt über eine vereinheitlichte Konsole für das Sicherheitsmanagement, mit der Sie die Sicherheit von Azure-Ressourcen und Ihrer gehosteten Daten überwachen und verbessern können. Microsoft Defender für Cloud enthält Secure Score. Hierbei handelt es sich um eine Bewertung anhand einer Analyse des Status der Best Practices-Konfiguration aus Azure Advisor und der Gesamtkonformität von Azure Policy.

Microsoft Defender für Cloud bietet Azure-Kunden die folgenden Features:

* Sicherheitsrichtlinie, Bewertung und Empfehlungen
* Erfassung von Sicherheitsereignissen und Suche
* Zugriffs- und Anwendungssteuerungen
* Erweiterte Bedrohungserkennung
* Just-In-Time-VM-Zugriffssteuerung
* Hybridsicherheit

Der Bereich der mit Azure Security Center überwachten Ressourcen kann so erweitert werden, dass er die unterstützten lokalen Ressourcen in einer Hybridcloudumgebung enthält. Hierzu gehören lokale Ressourcen, die derzeit von einer unterstützten Version von System Center Operations Manager überwacht werden.

Die erweiterten Sicherheitsfunktionen von Defender für Cloud bieten Cloud-basierte Sicherheitskontrollen, die von [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm) gefordert werden. Hierzu zählen das Filtern von Anwendungen und die Einschränkung von Administratorrechten per Just-In-Time-Zugriff.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../azure-monitor/overview.md) ist die zentrale Protokollierungslösung für alle Azure-Ressourcen und enthält Log Analytics und Application Insights. Für Azure-Ressourcen werden zwei wichtige Datentypen erfasst: Protokolle und Metriken. Nach der Erfassung in Azure Monitor können Protokollierungsinformationen von vielen verschiedenen Tools und zu unterschiedlichen Zwecken verwendet werden.

![Azure Monitor – Übersicht](media/overview.png)

Azure Monitor verfügt auch über das „Azure-Aktivitätsprotokoll“. Im Aktivitätsprotokoll werden alle Ereignisse gespeichert, die in Azure auf der Abonnementebene eintreten. Azure-Kunden erhalten zu Vorgängen, die für ihre Azure-Ressourcen durchgeführt werden, Hintergrundinformationen (wer, was und wann). Sowohl ressourcenbasierte Protokolle, die an Azure Monitor gesendet werden, als auch Azure-Aktivitätsprotokoll-Ereignisse können mit der integrierten Kusto-Abfragesprache analysiert werden. Diese Protokolle können dann exportiert, zum Erstellen von benutzerdefinierten Dashboards und Ansichten genutzt und so konfiguriert werden, dass Warnungen und Benachrichtigungen ausgelöst werden.

### <a name="azure-advisor"></a>Azure Advisor

Mit [Azure Advisor](../advisor/advisor-overview.md) werden unterstützte Azure-Ressourcen, vom System generierte Protokolldateien und aktuelle Ressourcenkonfigurationen in Ihrem Azure-Abonnement analysiert. Die in Azure Advisor bereitgestellte Analyse wird in Echtzeit generiert und basiert auf den von Microsoft empfohlenen bewährten Methoden. Alle unterstützten Azure-Ressourcen, die Ihrer Umgebung hinzugefügt werden, werden analysiert, und die entsprechenden Empfehlungen werden angegeben. Azure Advisor-Empfehlungen sind in vier Best Practices-Kategorien unterteilt:

* Sicherheit
* Hochverfügbarkeit
* Leistung
* Kosten

Die von Azure Advisor generierten Sicherheitsempfehlungen sind Teil der allgemeinen Sicherheitsanalyse von Microsoft Defender für Cloud.

Diese mit Azure Advisor gesammelten Informationen ermöglichen für Administratoren Folgendes:

* Erkenntnisse zu Ressourcenkonfigurationen, die nicht den bewährten Methoden entsprechen
* Anleitung zu bestimmten Problembehebungsaktionen
* Ranglisten mit Hinweisen, welche Problembehebungsaktionen eine hohe Priorität haben

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) ermöglicht die Anwendung von Regeln, mit denen die Arten von Azure-Ressourcen und die zulässige Konfiguration festgelegt werden. Azure Policy kann zum Steuern der Erstellung und Konfiguration von Ressourcen oder zum Überprüfen von Konfigurationseinstellungen in einer Umgebung verwendet werden. Diese Überprüfungsergebnisse können als Grundlage für die Problembehebungsaktionen verwendet werden. Azure Policy unterscheidet sich von der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC). Azure Policy wird zum Einschränken von Ressourcen und ihrer Konfiguration genutzt, und per Azure RBAC wird der privilegierte Zugriff auf Azure-Benutzer eingeschränkt.

Unabhängig davon, ob eine Richtlinie erzwungen oder ihre Auswirkung überprüft wird, wird die Konformität ständig überwacht. Außerdem werden für Administratoren die allgemeinen und ressourcenspezifischen Konformitätsinformationen bereitgestellt. Azure Policy Compliance-Daten werden Microsoft Defender für Cloud zur Verfügung gestellt und sind Teil des  Sicherheitswertes.

## <a name="key-design-considerations"></a>Wichtige Entwurfsaspekte

In Bezug auf die Implementierung einer Strategie für die Ereignisprotokollierung wird im ISM-Dokument des ACSC auf die folgenden zu berücksichtigenden Aspekte hingewiesen:

* Zentralisierte Protokollierung
* Protokollierung bestimmter Ereignisse
* Schutz von Ereignisprotokollen
* Aufbewahrung von Ereignisprotokollen
* Überprüfung von Ereignisprotokollen

Zusätzlich zur Erfassung und Verwaltung von Protokollen wird im ISM-Dokument auch die routinemäßige Sicherheitsrisikobewertung für die IT-Umgebung einer Organisation empfohlen.

### <a name="centralised-logging"></a>Zentralisierte Protokollierung

Bei allen Protokollierungslösungen sollten nach Möglichkeit erfasste Protokolle in einem zentralen Datenrepository zusammengefasst werden. So wird nicht nur die Komplexität des Betriebs reduziert und die Erstellung mehrerer Datensilos verhindert. Es können auch Daten aus mehreren Quellen zusammen analysiert werden, sodass korrelierende Ereignisse identifiziert werden können. Dies ist wichtig, um den Umfang von Cybersicherheitsvorfällen erkennen und verwalten zu können.

Diese Anforderung wird für alle Azure-Kunden mit Azure Monitor erfüllt. Mit diesem Angebot wird nicht nur in Azure ein zentrales Protokollrepository für alle Azure-Ressourcen bereitgestellt, sondern Sie können Ihre Daten auch an Azure Event Hubs streamen. Azure Event Hubs verfügt über einen vollständig verwalteten Dienst für die Datenerfassung in Echtzeit. Nachdem Azure Monitor-Daten an einen Azure Event Hub gestreamt wurden, können diese auch leicht mit vorhandenen unterstützten SIEM-Repositorys (Security Information & Event Management) und weiteren Überwachungstools von Drittanbietern verknüpft werden.

Auch Microsoft verfügt über eine eigene Azure-native SIEM-Lösung: Microsoft Sentinel. Microsoft Sentinel unterstützt viele verschiedene Datenconnectors und kann genutzt werden, um die Sicherheitsereignisse eines gesamten Unternehmens zu überwachen. Durch die Kombination von Daten aus unterstützten [Datenkonnektoren](../sentinel/connect-data-sources.md), dem in Microsoft Sentinel integrierten maschinellen Lernen und der Abfragesprache Kusto erhalten Sicherheitsadministratoren eine einzige Lösung für die Erkennung von Warnungen, die Transparenz von Bedrohungen, die proaktive Suche und die Reaktion auf Bedrohungen. Microsoft Sentinel bietet außerdem eine Jagd- und Notizbuchfunktion, die es Sicherheitsadministratoren ermöglicht, alle im Rahmen einer Sicherheitsuntersuchung durchgeführten Schritte in einem wiederverwendbaren Playbook aufzuzeichnen, das innerhalb einer Organisation gemeinsam genutzt werden kann. Sicherheitsadministratoren können sogar die integrierte [Benutzeranalyse](../sentinel/overview.md) verwenden, um die Aktionen eines bestimmten einzelnen Benutzers zu untersuchen.

### <a name="logged-events-and-log-detail"></a>Protokollierte Ereignisse und Protokolldetails

Das ISM-Dokument (Handbuch zur Informationssicherheit) enthält eine ausführliche Liste mit Ereignisprotokolltypen, die Teil einer Strategie für die Protokollierung sein sollten. Alle erfassten Protokolle müssen ausreichend viele Details enthalten, damit sie beim Durchführen von Analysen und Untersuchungen einen praktischen Nutzen haben.

Die in Azure erfassten Protokolle fallen unter eine der drei folgenden Kategorien:

* **Steuerungs- und Verwaltungsprotokolle**: Diese Protokolle enthalten Informationen zu CREATE-, UPDATE- und DELETE-Vorgängen in Azure Resource Manager.

* **Datenebenenprotokolle**: Diese Protokolle enthalten Ereignisse, die im Rahmen der Azure-Ressourcennutzung ausgelöst werden. Dies betrifft auch Quellen wie Windows-Ereignisprotokolle, z. B. System-, Sicherheits- und Anwendungsprotokolle.

* **Verarbeitete Ereignisse**: Diese Ereignisse enthalten Informationen zu Ereignissen und Warnungen, die im Namen des Kunden automatisch von Azure verarbeitet wurden. Ein Beispiel für ein verarbeitetes Ereignis ist eine Microsoft Defender für Cloud-Warnung.

Die Überwachung von virtuellen Azure-Computern wird durch die Bereitstellung des VM-Agents für Windows und Linux erweitert. Der Umfang der erfassten Protokollierungsinformationen wird so erheblich vergrößert. Die Bereitstellung dieses Agenten kann so konfiguriert werden, dass sie automatisch über den Microsoft Defender für Cloud erfolgt.

Microsoft stellt ausführliche Informationen zu spezifischen Protokollen für Azure-Ressourcen und den zugehörigen [Schemas](../security/fundamentals/log-audit.md) bereit.

### <a name="log-retention-and-protection"></a>Protokollaufbewahrung und -schutz

 Ereignisprotokolle müssen im erforderlichen Aufbewahrungszeitraum sicher gespeichert werden. Im ISM-Dokument wird empfohlen, Protokolle mindestens sieben Jahre lang aufzubewahren. In Azure gibt es verschiedene Möglichkeiten, wie Sie für die erfassten Protokolle eine lange Lebensdauer sicherstellen können. Standardmäßig werden die Azure-Protokollereignisse 90 Tage lang gespeichert. Mit Azure Monitor erfasste Protokolldaten können verschoben und unter einem Azure Storage-Konto so lange gespeichert werden, wie dies erforderlich ist. Unter einem Azure Storage-Konto gespeicherte Aktivitätsprotokolle können eine bestimmte Anzahl von Tagen oder bei Bedarf auch auf unbestimmte Zeit aufbewahrt werden.

Azure Storage-Konten zum Speichern von Azure-Protokollereignissen können georedundant gemacht und mit Azure Backup gesichert werden. Nach der Erfassung mit Azure Backup wird für alle Löschvorgänge für Sicherungen, die Protokolle enthalten, eine Genehmigung des Administrators benötigt, und für das Löschen gekennzeichnete Sicherungen werden noch 14 Tage lang aufbewahrt, um die Wiederherstellung zu ermöglichen. Azure Backup ermöglicht 9.999 Kopien einer geschützten Instanz, also mehr als 27 Jahre an täglichen Sicherungen.

Die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) sollte genutzt werden, um den Zugriff auf Ressourcen zu steuern, die für die Azure-Protokollierung verwendet werden. Azure Monitor, Azure Storage-Konten und Azure Backup-Sicherungen sollten mit Azure RBAC konfiguriert werden, um die Sicherheit der in den Protokollen enthaltenen Daten sicherzustellen.

### <a name="log-auditing"></a>Protokollüberprüfung

Der wahre Wert von Protokollen zeigt sich erst, wenn diese analysiert werden. Wenn Sie sowohl die automatisierte als auch die manuelle Analyse verwenden und mit den verfügbaren Tools vertraut sind, ist dies hilfreich, um Verstöße gegen die Sicherheitsrichtlinie des Unternehmens und Cybersicherheitsvorfälle zu erkennen und zu managen. Azure Monitor verfügt über einen umfangreichen Satz mit Tools zum Analysieren von erfassten Protokollen. Das Ergebnis dieser Analyse kann dann von Systemen gemeinsam genutzt, visualisiert oder in mehreren Formaten verbreitet werden.

In Azure Monitor gespeicherte Protokolldaten werden in einem Log Analytics-Arbeitsbereich aufbewahrt. Alle Analysen beginnen mit einer Abfrage. Azure Monitor-Abfragen sind in der Kusto-Abfragesprache geschrieben. Abfragen sind die Grundlage aller Ausgaben von Azure Monitor: von Azure-Dashboards bis hin zu Warnungsregeln.

![Übersicht über Azure-Protokollabfragen](media/queries-overview.png)

Die Überprüfung von Protokollen kann durch die Verwendung von Überwachungslösungen verbessert werden. Hierbei handelt es sich um fertige Lösungspakete, die Erfassungslogik, Abfragen und Ansichten zur Datenvisualisierung enthalten. Microsoft stellt verschiedene [Überwachungslösungen](../azure-monitor/monitor-reference.md) bereit, und weitere Lösungen von Produktanbietern finden Sie auf dem Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Sicherheitsrisikobewertung und -management

Im ISM-Dokument wird darauf hingewiesen, dass die routinemäßige Bewertung und das Management von Sicherheitsrisiken sehr wichtig sind. Ihre IT-Umgebung wird ständig weiterentwickelt, und dies gilt auch für die externen Sicherheitsbedrohungen. Mit  Microsoft Defender für Cloud können Sie automatisierte Bewertungen von Sicherheitsrisiken erstellen. Es ist auch eine Anleitung vorhanden, wie Sie Aktivitäten zur Problembehebung planen und durchführen.

Mit Secure Score in Microsoft Defender für Cloud erhalten Sie eine Liste mit Empfehlungen, mit denen Sie die Sicherheit Ihrer Umgebung verbessern können, indem Sie diese anwenden. Die Liste ist nach der Auswirkung auf die Secure Score-Gesamtbewertung sortiert (absteigend von der stärksten Auswirkung). Die Sortierung der Liste nach der Auswirkung ermöglicht Ihnen die Konzentration auf die Empfehlungen mit der höchsten Priorität, die für die Verbesserung der Sicherheit den größten Nutzen haben.

Azure Policy ist auch ein wichtiger Bestandteil der fortlaufenden Sicherheitsrisikobewertung. Die Arten der unter Azure Policy verfügbaren Richtlinien reichen von der Durchsetzung von Ressourcentags und -werten bis zur Einschränkung der Azure-Regionen, in denen Ressourcen erstellt werden können, und der vollständigen Blockade der Erstellung bestimmter Ressourcentypen. Azure-Richtlinien können als Initiativen gruppiert werden. Initiativen werden verwendet, um zusammengehörige Azure-Richtlinien anzuwenden. Wenn diese Richtlinien gemeinsam als Gruppe angewendet werden, bilden sie die Grundlage eines bestimmten Sicherheits- oder Konformitätsziels.

Azure Policy verfügt über eine Bibliothek mit integrierten Richtliniendefinitionen, die ständig erweitert wird. Außerdem haben Sie im Azure-Portal die Möglichkeit, Ihre eigenen benutzerdefinierten Azure Policy-Definitionen zu erstellen. Nachdem Sie eine Richtlinie in der vorhandenen Bibliothek gefunden oder eine neue Richtlinie erstellt haben, können Sie die Richtlinie den Azure-Ressourcen zuweisen. Für diese Zuweisungen kann eine [Bereichseinteilung](../governance/policy/tutorials/create-and-manage.md) auf verschiedenen Ebenen in der Hierarchie für die Ressourcenverwaltung vorgenommen werden. Die Richtlinienzuweisung wird vererbt. Dies bedeutet, dass alle untergeordneten Ressourcen in einem Bereich die gleiche Richtlinienzuweisung erhalten. Außerdem können Ressourcen bei Bedarf auch von der bereichsbezogenen Richtlinienzuweisung ausgeschlossen werden.

Alle bereitgestellten Azure-Richtlinien gehen in die Secure Score-Bewertung einer Organisation ein. In einer hochgradig angepassten Umgebung können benutzerdefinierte Azure Policy-Definitionen erstellt und bereitgestellt werden, um Überwachungsinformationen zu erhalten, die auf bestimmte Workloads zugeschnitten sind.

## <a name="getting-started"></a>Erste Schritte

Um mit Microsoft Defender für Cloud zu beginnen und Azure Monitor, Advisor und Policy in vollem Umfang nutzen zu können, empfiehlt Microsoft die folgenden ersten Schritte:

* Aktivieren von Microsoft Defender für Cloud
* Aktivieren Sie die erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud
* Aktivieren Sie die automatische Bereitstellung des Log Analytics-Agenten auf unterstützten Rechnern
* Überprüfung, Priorisierung und Entschärfung der Sicherheitsempfehlungen und -warnungen, die in den Dashboards von Defender für Cloud angezeigt werden

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Azure Policy und Azure Blueprints](azure-policy.md), um ausführliche Informationen zu erhalten, wie Sie Governance und Kontrolle für Ihre Ressourcen in Azure Australien implementieren, um die Einhaltung von Richtlinien und Bestimmungen sicherzustellen.
