---
title: Normalisierung und das Advanced SIEM Information Model (ASIM) | Microsoft Docs
description: Dieser Artikel erklärt, wie Microsoft Sentinel Daten aus vielen verschiedenen Quellen mit Hilfe des Advanced SIEM Information Model (ASIM) normalisiert.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: f01526c598b0d890dc40f591f825e10e6060cf8c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522534"
---
# <a name="normalization-and-the-advanced-siem-information-model-asim-public-preview"></a>Normalisierung und das Advanced SIEM Information Model (ASIM) (Öffentliche Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel nimmt Daten aus vielen Quellen auf. Die gemeinsame Verwendung verschiedener Datentypen und -tabellen macht es notwendig, dass Sie mit den einzelnen Typen und Tabellen vertraut sind und eindeutige Datensätze für Analyseregeln, Arbeitsmappen und Hunting-Abfragen für jeden Typ und jedes Schema schreiben und verwenden.


Manchmal benötigen Sie separate Regeln, Arbeitsmappen und Abfragen, selbst bei in den Datentypen gemeinsam verwendeten Elementen, z. B. Firewallgeräten. Auch die Korrelation zwischen verschiedenen Datentypen bei einer Untersuchung und Hunting-Abfrage kann schwierig sein.

Dieser Artikel gibt einen Überblick über das Advanced Security Information and Event Management (SIEM) Information Model (ASIM), das eine Lösung für die Herausforderungen bei der Handhabung mehrerer Datentypen bietet.

> [!TIP]
> Sehen Sie sich auch das [ASIM-Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) an, oder befassen Sie sich mit den [Webinarfolien](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG). Weitere Informationen finden Sie in den [nächsten Schritten](#next-steps).
>

> [!IMPORTANT]
> ASIM befindet sich derzeit in der Vorschauphase. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="common-asim-usage"></a>Allgemeine ASIM-Verwendung

Das Advanced SIEM Information Model (ASIM) bietet eine nahtlose Erfahrung für die Handhabung verschiedener Quellen in einheitlichen, normalisierten Ansichten, indem es die folgenden Funktionen bereitstellt:

- **Quellenübergreifende Erkennungsfunktion**. Normalisierte Analyseregeln funktionieren quellenübergreifend, lokal und in der Cloud und erkennen Angriffe wie Brute-Force-Angriffe oder unmögliche Ortswechsel auf verschiedenen Systemen, einschließlich Okta, AWS und Azure.

- **Quellenunabhängige Inhalte**. Die Abdeckung sowohl vorgefertigter als auch benutzerdefinierter Inhalte mithilfe von ASIM wird automatisch auf alle Quellen erweitert, die ASIM unterstützen. Dies gilt selbst dann, wenn die Quelle erst nach dem Erstellen des Inhalts hinzugefügt wurde. Die Prozessereignisanalyse unterstützt beispielsweise alle Quellen, die ein Kunde zum Importieren der Daten verwenden kann, z. B. Microsoft Defender für Endpunkt, Windows-Ereignisse und Sysmon.

- **Unterstützung für benutzerdefinierte Quellen** in integrierten Analysen

- **Benutzerfreundlichkeit**. Nachdem ein Analyst ASIM eingeführt hat, ist das Schreiben von Abfragen viel einfacher, weil die Feldnamen immer identisch sind.

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM und die Metadaten von Open-Source-Sicherheitsereignissen

Das Advanced SIEM Information Model ist mit dem [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) gemeinsamen Informationsmodell abgestimmt und ermöglicht eine vorhersehbare Korrelation von Entitäten über normalisierte Tabellen hinweg.

OSSEM ist ein von der Community betriebenes Projekt, das hauptsächlich auf die Dokumentation und Standardisierung von Sicherheitsereignisprotokollen aus unterschiedlichen Datenquellen und Betriebssystemen ausgerichtet ist. Außerdem stellt das Projekt ein Common Information Model (CIM) bereit, das während der Datennormalisierungsprozeduren von Datentechnikern verwendet werden kann, damit Sicherheitsanalytiker Daten über verschiedene Datenquellen hinweg abfragen und analysieren können.

Weitere Informationen finden Sie in der [OSSEM-Referenzdokumentation](https://ossemproject.com/cdm/guidelines/entity_structure.html).

## <a name="asim-components"></a>ASIM-Komponenten

Die folgende Abbildung zeigt, wie nicht normalisierte Daten in normalisierte Inhalte übersetzt und in Microsoft Sentinel verwendet werden können. Beispielsweise können Sie eine benutzerdefinierte, produktspezifische, nicht normalisierte Tabelle mithilfe eines Parsers und eines Normalisierungsschemas in normalisierte Daten konvertieren. Die normalisierten Daten können Sie in von Microsoft definierten sowie in benutzerdefinierten Analysen, Regeln, Arbeitsmappen, Abfragen usw. verwenden.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Nicht normalisierter in normalisierter Datenkonvertierungsfluss und -nutzung in Microsoft Sentinel":::

Das Advanced SIEM Information Model umfasst die folgenden Komponenten:

|Komponente  |BESCHREIBUNG  |
|---------|---------|
|**Normalisierte Schemas**     |   Standardsätze von vorhersagbaren Ereignistypen, die Sie beim Erstellen einheitlicher Funktionen verwenden können. <br><br>Jedes Schema definiert die Felder, die ein Ereignis, eine Namenskonvention für normalisierte Spalten und ein Standardformat für die Feldwerte darstellen. <br><br> In ASIM sind derzeit die folgenden Schemas definiert:<br> - [Netzwerksitzung](./network-normalization-schema.md)<br> - [DNS-Aktivität](dns-normalization-schema.md)<br> - [Prozessereignis](process-events-normalization-schema.md)<br> - [Authentifizierungsereignis](authentication-normalization-schema.md)<br> - [Registrierungsereignis](registry-event-normalization-schema.md)<br> - [Dateiaktivität](file-event-normalization-schema.md)  <br><br>Weitere Informationen finden Sie unter [Advanced SIEM-Informationsmodell-Schemata](normalization-about-schemas.md).  |
|**Parser**     |  Zuordnung vorhandener Daten unter Verwendung von [KQL-Funktionen](/azure/data-explorer/kusto/query/functions/user-defined-functions) zu normalisierten Schemas. <br><br>Stellen Sie die von Microsoft entwickelten Normalisierungsparser aus dem Ordner [`Parsers` im Microsoft Sentinel GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers) bereit. Normalisierte Parser befinden sich in den Unterordnern, die mit **ASim*** beginnen.  <br><br>Weitere Informationen finden Sie unter [Advanced SIEM-Informationsmodell-Parser](normalization-about-parsers.md).     |
|**Inhalte für jedes normalisierte Schema**     |    Enthält Analyseregeln, Arbeitsmappen, Hunting-Abfragen und mehr. Die Inhalte für jedes normalisierte Schema können für alle normalisierten Daten verwendet werden, ohne dass quellenspezifische Inhalte erstellt werden müssen. <br><br>Weitere Informationen finden Sie unter [Advanced SIEM-Informationsmodell Inhalt](normalization-content.md).   |
| | |

### <a name="asim-terminology"></a>ASIM-Terminologie

Das Advanced SIEM Information Model verwendet die folgenden Begriffe:

|Begriff  |BESCHREIBUNG  |
|---------|---------|
|**Meldendes Gerät**     |   Das System, das die Datensätze an Microsoft Sentinel sendet. Möglicherweise ist dieses System nicht das Zielsystem für den gesendeten Datensatz.      |
|**Datensatz**     |Eine Dateneinheit, die vom meldenden Gerät gesendet wird. Ein Datensatz wird häufig als `log`, `event` oder `alert` angegeben, kann sich aber auch auf andere Datentypen beziehen.         |
|**Inhalt** oder **Inhaltselement**     |Die verschiedenen, anpassbaren oder vom Benutzer erstellten Artefakte, die mit Microsoft Sentinel verwendet werden können. Zu diesen Artefakten gehören beispielsweise Analyseregeln, Hunting-Abfragen und Arbeitsmappen. Ein Inhaltselement ist ein solches Artefakt.|
| | |

<br>

## <a name="getting-started-with-asim"></a>Erste Schritte mit ASIM

So beginnen Sie mit der Verwendung von ASIM:

1. Stellen Sie alle ASIM-Parser schnell aus dem [Microsoft Sentinel GitHub-Repository](https://aka.ms/AzSentinelASim) bereit.

1. Aktivieren Sie Analyseregelvorlagen, die ASIM verwenden. Weitere Informationen finden Sie in der Inhaltsliste [Advanced SIEM Information Model (ASIM)](normalization-content.md#builtin).

1. Verwenden Sie ASIM anhand der folgenden Methoden in Ihrem Arbeitsbereich:

    - Verwenden Sie die ASIM-Jagdabfragen aus dem Microsoft Sentinel GitHub-Repository, wenn Sie Protokolle in KQL auf der Seite Microsoft Sentinel **Logs** abfragen. Weitere Informationen finden Sie in der Inhaltsliste [Advanced SIEM Information Model (ASIM)](normalization-content.md#builtin).

    - Schreiben Sie mithilfe von ASIM Ihre eigenen Analyseregeln, oder [konvertieren Sie vorhandene](normalization-content.md#builtin).

    - Ermöglichen Sie die Verwendung integrierter Analysefunktionen für Ihre benutzerdefinierten Daten, indem Sie Parser für Ihre benutzerdefinierten Quellen [schreiben](normalization-about-parsers.md) und sie dem relevanten quellenunabhängigen Parser [hinzufügen](normalization-about-parsers.md#include).

## <a name="next-steps"></a><a name="next-steps"></a>Nächste Schritte

Dieser Artikel gibt einen Überblick über die Normalisierung in Microsoft Sentinel und das Advanced SIEM Information Model.

Weitere Informationen finden Sie unter

- Sehen Sie sich das [ASIM-Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) an, oder befassen Sie sich mit den [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG).
- [Advanced SIEM-Informationsmodell-Schemata](normalization-about-schemas.md)
- [Advanced SIEM-Informationsmodell-Parser](normalization-about-parsers.md)
- [Advanced SIEM-Informationsmodell Inhalt](normalization-content.md)
