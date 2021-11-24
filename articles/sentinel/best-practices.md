---
title: Bewährte Verfahren für Microsoft Sentinel
description: Erfahren Sie mehr über die besten Praktiken bei der Verwaltung Ihres Microsoft Sentinel-Arbeitsbereichs.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 15543989655bf094f221a4259ad0b3e765338329
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524700"
---
# <a name="best-practices-for-microsoft-sentinel"></a>Bewährte Verfahren für Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Diese Sammlung von Best Practices bietet Anleitungen für die Bereitstellung, Verwaltung und Verwendung von Microsoft Sentinel, einschließlich Links zu anderen Artikeln für weitere Informationen.

> [!IMPORTANT]
> Bevor Sie Microsoft Sentinel bereitstellen, sollten Sie die [Vorbereitungsaktivitäten und -voraussetzungen](prerequisites.md) überprüfen und abschließen.
>


## <a name="best-practice-references"></a>Referenzen für bewährte Praktiken

In der Microsoft Sentinel-Dokumentation finden Sie in verschiedenen Artikeln Hinweise zu bewährten Verfahren. Zusätzlich zu den Inhalten in diesem Artikel finden Sie weitere Informationen in den folgenden Artikeln:

- **Administratorbenutzer**:

    - [Aktivitäten vor der Bereitstellung und Voraussetzungen für die Bereitstellung von Microsoft Sentinel](prerequisites.md)
    - [Bewährte Methoden für die Microsoft Sentinel-Arbeitsbereichsarchitektur](best-practices-workspace-architecture.md)
    - [Entwerfen der Microsoft Sentinel-Arbeitsbereichsarchitektur](design-your-workspace-architecture.md)
    - [Microsoft Sentinel sample workspace designs](sample-workspace-designs.md)
    - [Bewährte Methoden für die Datensammlung](best-practices-data.md)
    - [Microsoft Sentinel Kosten und Abrechnung](azure-sentinel-billing.md)
    - [Berechtigungen in Microsoft Sentinel](roles.md)
    - [Schutz des geistigen Eigentums von MSSP in Microsoft Sentinel](mssp-protect-intellectual-property.md)
    - [Integration von Threat Intelligence in Microsoft Sentinel](threat-intelligence-integration.md)
    - [Microsoft Sentinel-Abfragen und -Aktivitäten prüfen](audit-sentinel-data.md)

- **Analysten**:

    - [Empfohlene Playbooks](automate-responses-with-playbooks.md#recommended-playbooks)
    - [Handhabung von Fehlalarmen in Microsoft Sentinel](false-positives.md)
    - [Jagd auf Bedrohungen mit Microsoft Sentinel](hunting.md)
    - [Häufig verwendete Microsoft Sentinel-Arbeitsmappen](top-workbooks.md)
    - [Standardmäßig verfügbare Erkennung von Bedrohungen](detect-threats-built-in.md)
    - [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md)
    - [Aufspüren von Sicherheitsrisiken mit Jupyter Notebook](notebooks.md)

Weitere Informationen finden Sie auch in unserem Video: [Architecting SecOps for Success: Best Practices for Deploying Microsoft Sentinel](https://youtu.be/DyL9MEMhqmI)

## <a name="regular-soc-activities-to-perform"></a>Durchzuführende reguläre SOC-Aktivitäten

Planen Sie die folgenden Microsoft Sentinel-Aktivitäten regelmäßig ein, um eine kontinuierliche Anwendung bewährter Sicherheitsverfahren zu gewährleisten:

### <a name="daily-tasks"></a>Tägliche Aufgaben

- **Selektieren und Untersuchen von Incidents**.  Überprüfen Sie die Seite **Vorfälle** von Microsoft Sentinel, um nach neuen Vorfällen zu suchen, die von den derzeit konfigurierten Analyseregeln generiert wurden, und beginnen Sie mit der Untersuchung neuer Vorfälle. Weitere Informationen finden Sie unter [Tutorial: Untersuchen von Vorfällen mit Microsoft Sentinel](investigate-cases.md).

- **Untersuchen Sie Hunting-Abfragen und Textmarken**. Untersuchen Sie die Ergebnisse für alle integrierten Abfragen, und aktualisieren Sie vorhandene Hunting-Abfragen und Textmarken. Generieren Sie manuell neue Incidents, oder aktualisieren Sie ggf. alte Incidents.  Weitere Informationen finden Sie unter

    - [Automatisches Erstellen von Incidents aus Microsoft-Sicherheitswarnungen](create-incidents-from-alerts.md)
    - [Jagd auf Bedrohungen mit Microsoft Sentinel](hunting.md)
    - [Verfolgen Sie Daten während der Jagd mit Microsoft Sentinel](bookmarks.md)

- **Analyseregeln**.  Überprüfen und aktivieren Sie ggf. neue Analyseregeln, einschließlich neu veröffentlichter oder neu verfügbarer Regeln von kürzlich verbundenen Datenconnectors.

- **Datenconnectors**. Überprüfen Sie den Status, das Datum und die Uhrzeit des letzten Protokolls, das von jedem Datenconnector empfangen wurde, um sicherzustellen, dass Daten fließen. Überprüfen Sie, ob neue Connectors verfügbar sind, und überprüfen Sie die Erfassung, um sicherzustellen, dass die festgelegten Grenzwerte nicht überschritten wurden. Weitere Informationen finden Sie unter [Bewährte Methoden für Datensammlungen](best-practices-data.md) und [Verbinden von Datenquellen](connect-data-sources.md).

- **Log Analytics-Agent** Stellen Sie sicher, dass Server und Arbeitsstationen aktiv mit dem Arbeitsbereich verbunden sind, und behandeln und beheben Sie Verbindungsfehler.   Weitere Informationen finden Sie unter [Log Analytics – Übersicht über Agents](../azure-monitor/agents/log-analytics-agent.md).

- **Playbookfehler**. Überprüfen Sie den Status der Playbook-Ausführung, und beheben Sie Fehler.   Weitere Informationen finden Sie unter [Tutorial: Verwendung von Playbooks mit Automatisierungsregeln in Microsoft Sentinel](tutorial-respond-threats-playbook.md).

### <a name="weekly-tasks"></a>Wöchentliche Aufgaben

- **Arbeitsmappenaktualisierungen**. Überprüfen Sie, ob Arbeitsmappen über Aktualisierungen verfügen, die installiert werden müssen. Weitere Informationen finden Sie unter [Häufig verwendete Microsoft Sentinel-Arbeitsmappen](top-workbooks.md).

- **Microsoft Sentinel GitHub Repository Überprüfung**. Prüfen Sie das [Microsoft Sentinel GitHub-Repository](https://github.com/Azure/Azure-Sentinel), um zu sehen, ob es neue oder aktualisierte Ressourcen gibt, die für Ihre Umgebung von Nutzen sind, wie z. B. Analyseregeln, Arbeitsmappen, Jagdabfragen oder Playbooks.

- **Microsoft Sentinel-Überprüfung**. Überprüfen Sie die Aktivitäten von Microsoft Sentinel, um festzustellen, wer Ressourcen wie Analyseregeln, Lesezeichen usw. aktualisiert oder gelöscht hat. Weitere Informationen finden Sie unter [Audit Microsoft Sentinel Abfragen und Aktivitäten](audit-sentinel-data.md).

### <a name="monthly-tasks"></a>Monatliche Aufgaben

- **Überprüfen Sie den Benutzerzugriff**. Überprüfen Sie Berechtigungen für Ihre Benutzer, und prüfen Sie auf inaktive Benutzer. Weitere Informationen finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).

- **Überprüfen des Log Analytics-Arbeitsbereichs**. Überprüfen Sie, ob die Datenaufbewahrungsrichtlinie des Log Analytics-Arbeitsbereichs weiterhin mit der Richtlinie Ihrer Organisation in Einklang steht.  Weitere Informationen finden Sie unter [Datenaufbewahrungsrichtlinie](/workplace-analytics/privacy/license-expiration) und [Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung](store-logs-in-azure-data-explorer.md).


## <a name="integrate-with-microsoft-security-services"></a>Integrieren in Microsoft-Sicherheitsdienste

Microsoft Sentinel wird durch die Komponenten gestärkt, die Daten an Ihren Arbeitsbereich senden, und wird durch die Integration mit anderen Microsoft-Diensten gestärkt. Alle Protokolle, die in Produkte wie Microsoft Defender for Cloud Apps, Microsoft Defender for Endpoint und Microsoft Defender for Identity eingespeist werden, ermöglichen es diesen Diensten, Erkennungen zu erstellen und diese Erkennungen wiederum an Microsoft Sentinel weiterzuleiten. Protokolle können auch direkt in Microsoft Sentinel eingelesen werden, um ein umfassenderes Bild von Ereignissen und Vorfällen zu erhalten.

Die folgende Abbildung zeigt zum Beispiel, wie Microsoft Sentinel Daten von anderen Microsoft-Diensten und Multi-Cloud- und Partnerplattformen aufnimmt, um Ihre Umgebung abzudecken:

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="Microsoft Sentinel integriert sich mit anderen Microsoft- und Partnerdiensten":::

Microsoft Sentinel ist mehr als nur das Erfassen von Warnungen und Protokollen aus anderen Quellen, sondern auch:

- **Verwendet die Informationen, die erfasst werden, mit [maschinellem Lernen](bring-your-own-ml.md)** , um bessere Ereigniskorrelation, Warnungsaggregation, Anomalieerkennung und vieles mehr zu ermöglichen.
- **Erstellt und präsentiert interaktive visuelle Elemente über [Arbeitsmappen](get-visibility.md)** und zeigt Trends, verwandte Informationen und wichtige Daten an, die sowohl für Administratoraufgaben als auch für Untersuchungen verwendet werden.
- **Führt [Playbooks](tutorial-respond-threats-playbook.md) aus, um auf Warnungen zu reagieren**, Informationen zu sammeln, Aktionen für Elemente durchzuführen und Benachrichtigungen an verschiedene Plattformen zu senden.
- **Integration in Partnerplattformen** wie ServiceNow und Jira, um wichtige Dienste für SOC-Teams zu bieten.
- **Erfassung und Abruf von Anreicherungsfeeds** von [Threat Intelligence-Plattformen](threat-intelligence-integration.md), um wertvolle Daten für die Untersuchung zu erhalten.

##  <a name="manage-and-respond-to-incidents"></a>Anzeigen von und Reagieren auf Incidents

Die folgende Abbildung zeigt die empfohlenen Schritte in einem Incident Management- und Reaktionsprozess.

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="Incident Management-Prozess: Selektierung. Vorbereitung. Korrektur. Beseitigung. Aktivitäten nach Incidents.":::

Die folgenden Abschnitte enthalten allgemeine Beschreibungen zur Verwendung der Azure Sentinel-Funktionen für Incident Management und Reaktion während des gesamten Prozesses. Weitere Informationen finden Sie unter [Tutorial: Untersuchen von Vorfällen mit Microsoft Sentinel](investigate-cases.md).

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>Verwenden der Seite „Incidents“ und des Untersuchungsdiagramms

Starten Sie einen beliebigen Triage-Prozess für neue Vorfälle auf der Seite Microsoft Sentinel **Vorfälle** in Microsoft Sentinel und dem **Untersuchungsdiagramm**. 

Entdecken Sie wichtige Entitäten wie Konten, URLs, IP-Adressen, Hostnamen, Aktivitäten, Zeitachsen und vieles mehr. Verwenden Sie diese Daten, um zu verstehen, ob ein [falsch positives](false-positives.md) Ergebnis vorliegt. In diesem Fall können Sie den Incident direkt schließen.

Alle generierten Incidents werden auf der Seite **Incidents** angezeigt, die als zentraler Ort für die Selektierung und frühe Untersuchung dient. Auf der Seite **Incidents** werden der Titel, der Schweregrad und die zugehörigen Warnungen, Protokolle und relevanten Entitäten aufgeführt. Incidents bieten auch einen schnellen Einblick in gesammelte Protokolle und alle Tools im Zusammenhang mit dem Incident.

Die Seite **Incidents** arbeitet mit dem **Untersuchungsdiagramm** zusammen, einem interaktiven Tool, mit dem Benutzer eine Warnung untersuchen und genau analysieren können, um den gesamten Umfang eines Angriffs zu erkennen. Benutzer können dann eine Zeitachse von Ereignissen erstellen und das Ausmaß einer Bedrohungskette entdecken.

Wenn Sie feststellen, dass der Incident True Positive ist, können Sie direkt auf der Seite **Incidents** Maßnahmen ergreifen, um Protokolle, Entitäten und die Bedrohungskette zu untersuchen. Nachdem Sie die Bedrohung identifiziert und einen Aktionsplan erstellt haben, verwenden Sie andere Tools in Microsoft Sentinel und [andere Microsoft-Sicherheitsdienste](best-practices.md#integrate-with-microsoft-security-services), um die Untersuchung fortzusetzen.


### <a name="handle-incidents-with-workbooks"></a>Behandeln von Incidents mit Arbeitsmappen

Neben der [Visualisierung und Darstellung von Informationen und Trends](get-visibility.md) sind die Arbeitsmappen von Microsoft Sentinel wertvolle Ermittlungsinstrumente.

Verwenden Sie beispielsweise die Arbeitsmappe [Erkenntnisse aus Ermittlungen](top-workbooks.md#investigation-insights), um bestimmte Incidents zusammen mit allen zugeordneten Entitäten und Warnungen zu untersuchen. Mit dieser Arbeitsmappe können Sie Entitäten eingehender analysieren, indem Sie zugehörige Protokolle, Aktionen und Warnungen anzeigen.

### <a name="handle-incidents-with-threat-hunting"></a>Behandeln von Incidents mit Bedrohungssuche

Führen Sie beim Untersuchen und Suchen nach Ursachen integrierte Bedrohungssucheabfragen aus, und überprüfen Sie die Ergebnisse auf Anzeichen einer Gefährdung.

Verwenden Sie den [Livestream](livestream.md) während einer Untersuchung oder nachdem Sie Schritte zur Behebung und Beseitigung der Bedrohung unternommen haben, um in Echtzeit zu überwachen, ob noch böswillige Ereignisse vorhanden sind oder diese noch andauern.

### <a name="handle-incidents-with-entity-behavior"></a>Behandeln von Incidents mit Entitätsverhalten

Das Verhalten von Entitäten in Microsoft Sentinel ermöglicht es Benutzern, Aktionen und Warnungen für bestimmte Entitäten zu überprüfen und zu untersuchen, z. B. die Untersuchung von Konten und Hostnamen. Weitere Informationen finden Sie unter

- [ Aktivieren Sie User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel](enable-entity-behavior-analytics.md)
- [Untersuchen von Incidents mit UEBA-Daten](investigate-with-ueba.md)
- [Microsoft Sentinel UEBA Anreicherungen Referenz](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>Behandeln von Incidents mit Watchlists und Threat Intelligence

Um Erkennungen zu maximieren, die auf Threat Intelligence basieren, stellen Sie sicher, dass Sie [Threat Intelligence-Datenconnectors](connect-threat-intelligence-tip.md) verwenden, um Anzeichen für Kompromittierung zu erfassen:

- Verbinden von Datenquellen, die für die [Fusion](fusion.md)- und [TI Map](./understand-threat-intelligence.md)-Warnungen erforderlich sind
- Erfassen von Indikatoren von [TAXII- und TIP-Plattformen](./connect-threat-intelligence-tip.md)

Verwenden Sie Indikatoren für Kompromittierung in Analyseregeln, bei der Bedrohungssuche, beim Untersuchen von Protokollen oder Generieren von weiteren Incidents.

Verwenden Sie eine Watchlist, die Daten aus den erfassten Daten und externen Quellen kombiniert, z. B. Anreicherungsdaten. Erstellen Sie beispielsweise Listen mit IP-Adressbereichen, die von Ihrer Organisation oder kürzlich ausgeschiedenen Mitarbeitern verwendet werden. Verwenden Sie Watchlists mit Playbooks, um Anreicherungsdaten zu sammeln und schädliche IP-Adressen beispielsweise Watchlists hinzuzufügen, die während der Erkennung, Bedrohungssuche und bei Untersuchungen verwendet werden.

Verwenden Sie während eines Incidents Watchlists mit Untersuchungsdaten, und löschen Sie diese dann, wenn Ihre Untersuchung abgeschlossen ist, um sicherzustellen, dass vertrauliche Daten nicht angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit Microsoft Sentinel finden Sie unter:

- [ Integrierter Microsoft Sentinel](quickstart-onboard.md)
- [Einblick in Warnungen](get-visibility.md)
