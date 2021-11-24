---
title: Verwenden von Entitäten zum Klassifizieren und Analysieren von Daten in Microsoft Sentinel | Microsoft-Dokumentation
description: Weisen Sie Datenelementen in Microsoft Sentinel Entitätsklassifizierungen (Benutzer, Hostnamen, IP-Adressen) zu, und verwenden Sie diese, um Daten aus mehreren Quellen zu vergleichen, zu analysieren und zu korrelieren.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5fe74357b404b516f1fac5285bbca9d7fe5d2afb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521261"
---
# <a name="classify-and-analyze-data-using-entities-in-microsoft-sentinel"></a>Klassifizieren und Analysieren von Daten mithilfe von Entitäten in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="what-are-entities"></a>Was sind Entitäten?

Wenn Warnungen an Microsoft Sentinel gesendet oder von Microsoft Sentinel generiert werden, enthalten sie Datenelemente, die Sentinel erkennen und in Kategorien wie **Entitäten** einordnen kann. Wenn Microsoft Sentinel versteht, welche Art von Entität ein bestimmtes Datenelement darstellt, kennt Sentinel die richtigen Fragen, die es dazu gestellt werden müssen, und kann dann Erkenntnisse zu diesem Element über die gesamte Bandbreite von Datenquellen hinweg vergleichen, leicht nachverfolgen und während der gesamten Sentinel-Erfahrung darauf verweisen: Analysen, Untersuchungen, Abhilfemaßnahmen, Hunting usw. Einige gängige Beispiele für Entitäten sind Benutzer, Hosts, Dateien, Prozesse, IP-Adressen und URLs.

### <a name="entity-identifiers"></a>Entitätsbezeichner

Microsoft Sentinel unterstützt eine Vielzahl von Entitätstypen. Jeder Typ verfügt über eigene eindeutige Attribute, einschließlich einiger, mit denen eine bestimmte Entität identifiziert werden kann. Diese Attribute werden als Felder in der Entität dargestellt und als **Bezeichner** bezeichnet. Unten finden Sie die vollständige Liste der unterstützten Entitäten und ihrer Bezeichner.

#### <a name="strong-and-weak-identifiers"></a>Starke und schwache Bezeichner

Wie bereits erwähnt, gibt es für jeden Entitätstyp Felder oder Sätze von Feldern, die diesen identifizieren können. Diese Felder oder Gruppen von Feldern können als **starke Bezeichner** bezeichnet werden, wenn sie eine Entität eindeutig identifizieren können, oder als **schwache Bezeichner**, wenn sie eine Entität unter bestimmten Umständen identifizieren können, aber nicht garantiert wird, dass sie eine Entität in allen Fällen eindeutig identifizieren. In vielen Fällen kann jedoch eine Auswahl von schwachen Bezeichnern kombiniert werden, um einen starken Bezeichner zu erhalten.

Zum Beispiel können Benutzerkonten als **Kontoentitäten** auf mehr als eine Weise identifiziert werden: über einen einzelnen **starken Bezeichner** wie den numerischen Bezeichner eines Azure AD-Kontos (das Feld **GUID**) oder ihren Wert für den **Benutzerprinzipalnamen (User Principal Name, UPN)** oder alternativ über eine Kombination von **schwachen Bezeichnern**, etwa anhand der Felder **Name** und **NTDomain**. Verschiedene Datenquellen können denselben Benutzer auf unterschiedliche Weise identifizieren. Wenn Microsoft Sentinel auf zwei Entitäten stößt, die anhand ihrer Bezeichner als dieselbe Entität erkannt werden können, führt Sentinel die beiden Entitäten in einer einzigen Entität zusammen, damit diese ordnungsgemäß und konsistent behandelt werden kann.

Wenn jedoch einer Ihrer Ressourcenanbieter eine Warnung erstellt, in der eine Entität nicht ausreichend identifiziert ist (z. B. durch Verwendung nur eines einzelnen **schwachen Bezeichners** wie eines Benutzernamens ohne den Kontext des Domänennamens), dann kann die Benutzerentität nicht mit anderen Instanzen desselben Benutzerkontos gemergt werden. Diese anderen Instanzen würden als separate Entitäten identifiziert, und diese beiden Entitäten bleiben getrennt, anstatt vereinheitlicht zu werden.

Sie sollten sich vergewissern, dass alle Ihre Anbieter von Warnungen die Entitäten in den von ihnen erstellten Warnungen richtig identifizieren, um das Risiko eines solchen Ereignisses zu minimieren. Zusätzlich kann die Synchronisierung von Benutzerkontoentitäten mit Azure Active Directory ein vereinheitlichendes Verzeichnis erstellen, das in der Lage ist, Benutzerkontoentitäten zusammenzuführen.

#### <a name="supported-entities"></a>Unterstützte Entitäten

Die folgenden Arten von Entitäten werden derzeit in Microsoft Sentinel identifiziert:

- Benutzerkonto
- Host
- IP address (IP-Adresse)
- Malware
- Datei
- Prozess
- Cloudanwendung
- Domänenname
- Azure-Ressource
- Dateihash
- Registrierungsschlüssel
- Registrierungswert
- Sicherheitsgruppe
- URL
- IoT-Gerät
- Mailbox
- E-Mail-Cluster
- E-Mail
- Übermittlungs-E-Mail

Sie können die Bezeichner dieser Entitäten und andere relevante Informationen in der [Entitätenreferenz](entities-reference.md) einsehen.

## <a name="entity-mapping"></a>Entitätszuordnung

Wie erkennt Microsoft Sentinel ein Datenelement in einer Warnung als Identifizierung einer Entität?

Sehen wir uns an, wie die Datenverarbeitung in Microsoft Sentinel erfolgt. Daten werden aus verschiedenen Quellen über [Connectors](connect-data-sources.md) erfasst, sei es zwischen Diensten, Agent-basiert oder über einen Syslog-Dienst und eine Protokollweiterleitung. Die Daten werden in Tabellen in Ihrem Log Analytics Arbeitsbereich gespeichert. Diese Tabellen werden dann in regelmäßigen Intervallen anhand der von Ihnen definierten und aktivierten Analyseregeln abgefragt. Eine der zahlreichen Aktionen, die von diesen Analyseregeln ausgeführt werden, ist die Zuordnung von Datenfeldern in den Tabellen zu von Microsoft Sentinel erkannten Entitäten. Gemäß den Zuordnungen, die Sie in Ihren Analyseregeln definieren, verwendet Microsoft Sentinel Felder aus den von Ihrer Abfrage zurückgegebenen Ergebnissen, erkennt sie anhand der Bezeichner, die Sie für jeden Entitätstyp angegeben haben, und wendet auf sie den durch diese Bezeichner identifizierten Entitätstyp an.

Wozu das alles?

Wenn Microsoft Sentinel in der Lage ist, Entitäten in Warnungen aus verschiedenen Arten von Datenquellen zu identifizieren, und vor allem, wenn dies mit starken Bezeichnern erfolgen kann, die jeder Datenquelle oder einem dritten Schema gemeinsam sind, kann Sentinel dann leicht zwischen all diesen Warnungen und Datenquellen eine Korrelation herstellen. Diese Korrelationen helfen dabei, einen umfangreichen Speicher mit Informationen und Erkenntnissen zu den Entitäten zu erstellen, der Ihnen eine solide Grundlage für Ihre Sicherheitsmaßnahmen bietet.

Erfahren Sie, wie Sie [Datenfelder Entitäten zuordnen](map-data-fields-to-entities.md).

Erfahren Sie, [welche Bezeichner eine Entität stark identifizieren](entities-reference.md).

## <a name="entity-pages"></a>Entitätsseiten

Wenn Sie bei einer Suche, in einer Warnung oder bei einer Untersuchung auf eine Entität (derzeit nur Benutzer und Hosts) stoßen, können Sie die Entität auswählen und gelangen dadurch auf eine **Entitätsseite**. Hierbei handelt es sich um ein Datenblatt mit nützlichen Informationen zu dieser Entität. Zu den Informationen auf dieser Seite gehören grundlegende Fakten über die Entität, eine Zeitachse mit für diese Entität wichtigen Ereignissen sowie Erkenntnisse über das Verhalten der Entität.

Entitätsseiten bestehen aus drei Teilen:

- Der linke Bereich enthält Informationen zur Identifizierung der Entität, die über Datenquellen wie Azure Active Directory, Azure Monitor, Microsoft Defender für Cloud und Microsoft Defender für Cloud erfasst werden.

- Im mittleren Bereich wird eine grafische und textbezogene Zeitachse mit für die Entität wichtigen Ereignissen wie Warnungen, Lesezeichen und Aktivitäten angezeigt. Aktivitäten sind Aggregationen von wichtigen Ereignissen von Log Analytics. Die Abfragen, mit denen diese Aktivitäten erkannt werden, werden von Microsoft-Sicherheitsexpertenteams entwickelt.

- Im rechten Bereich werden Erkenntnisse über das Verhalten der Entität angezeigt. Mit diesen Erkenntnissen können Sie Anomalien und Sicherheitsbedrohungen schnell erkennen. Die Erkenntnisse werden von Microsoft-Sicherheitsexpertenteams entwickelt und basieren auf Anomalieerkennungsmodellen.

### <a name="the-timeline"></a>Die Zeitachse

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Zeitachse auf der Entitätsseite":::

Die Zeitachse ist ein wesentlicher Bestandteil des Beitrags der Entität zur Verhaltensanalyse in Microsoft Sentinel. Sie stellt den Verlauf von für eine Entität wichtigen Ereignissen dar, sodass Sie sich einen Überblick über die Aktivitäten der Entität innerhalb eines bestimmten Zeitraums verschaffen können.

Sie können den **Zeitbereich** aus verschiedenen vordefinierten Optionen (z. B. *letzte 24 Stunden*) auswählen oder einen benutzerdefinierten Zeitrahmen festlegen. Zudem können Sie Filter festlegen, mit denen die Informationen auf der Zeitachse auf bestimmte Ereignis- oder Warnungsarten beschränkt wird.

Die folgenden Elemente sind auf der Zeitachse enthalten:

- Warnungen: Benachrichtigungen, in denen die Entität als **zugeordnete Entität** definiert ist. Wenn Ihre Organisation [benutzerdefinierte Warnungen mit Analyseregeln](./detect-threats-custom.md) erstellt hat, müssen Sie darauf achten, dass die Entitätszuordnung der Regeln ordnungsgemäß vorgenommen wurde.

- Lesezeichen: Lesezeichen, die eine bestimmte Entität enthalten, die auf der Seite angezeigt wird

- Aktivitäten: Aggregation von für die Entität wichtigen Ereignissen

### <a name="entity-insights"></a>Erkenntnisse über Entitäten

Bei Erkenntnissen über Entitäten handelt es sich um Abfragen, die von Microsoft-Sicherheitsexperten definiert werden, um Ihren Analysten eine effizientere und effektivere Untersuchung zu ermöglichen. Die Erkenntnisse werden auf der Entitätsseite angezeigt. Sie stellen wichtige Sicherheitsinformationen über Hosts und Benutzer in Form von Tabellendaten und -diagrammen bereit. Dadurch, dass die Informationen hier angezeigt werden, müssen Sie keinen Umweg über Log Analytics machen. Zu den Erkenntnissen zählen Daten zu Anmeldungen, Gruppenerweiterungen, anomalen Ereignissen und vielem mehr sowie intelligente ML-Algorithmen zur Erkennung von anormalem Verhalten.

Die Erkenntnisse basieren auf den folgenden Datenquellen:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Microsoft Sentinel UEBA)
- Heartbeat (Azure Monitor Agent)
- CommonSecurityLog (Microsoft Sentinel)

### <a name="how-to-use-entity-pages"></a>Verwenden von Entitätsseiten

Entitätsseiten sind Teil von verschiedenen Verwendungsszenarios und können über Incident Management, das Untersuchungsdiagramm, Lesezeichen oder direkt über die Entitätssuchseite unter **User and Entity Behavior Analytics** im Hauptmenü von Microsoft Sentinel aufgerufen werden.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Anwendungsfälle für Entitätsseiten":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie mit Entitäten in Microsoft Sentinel arbeiten. Eine praktische Anleitung zur Implementierung und zur Verwendung der gewonnenen Erkenntnisse finden Sie in den folgenden Artikeln:

- [Aktivieren von User and Entity Behavior Analytics](./enable-entity-behavior-analytics.md) in Microsoft Sentinel
- [Aufspüren von Sicherheitsbedrohungen](./hunting.md)
