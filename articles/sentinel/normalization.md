---
title: Normalisierung und das Azure Sentinel-Informationsmodell (ASIM) | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie in Azure Sentinel Daten unter Verwendung des Azure Sentinel-Informationsmodells (ASIM) aus verschiedenen Quellen normalisiert werden.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: e03f343444aed0c3aafac28deccb0f38c35e2478
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515056"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim-public-preview"></a>Normalisierung und das Azure Sentinel-Informationsmodell (ASIM) (Öffentliche Vorschau)

In Azure Sentinel werden Daten aus vielen Quellen erfasst. Die gemeinsame Verwendung verschiedener Datentypen und -tabellen macht es notwendig, dass Sie mit den einzelnen Typen und Tabellen vertraut sind und eindeutige Datensätze für Analyseregeln, Arbeitsmappen und Hunting-Abfragen für jeden Typ und jedes Schema schreiben und verwenden.


Manchmal benötigen Sie separate Regeln, Arbeitsmappen und Abfragen, selbst bei in den Datentypen gemeinsam verwendeten Elementen, z. B. Firewallgeräten. Auch die Korrelation zwischen verschiedenen Datentypen bei einer Untersuchung und Hunting-Abfrage kann schwierig sein.

Dieser Artikel bietet eine Übersicht über das Azure Sentinel-Informationsmodell (ASIM), welches eine Lösung für die Herausforderungen bei der Behandlung mehrerer Datentypen bietet.

> [!TIP]
> Sehen Sie sich auch das [ASIM-Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) an, oder befassen Sie sich mit den [Webinarfolien](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG). Weitere Informationen finden Sie in den [nächsten Schritten](#next-steps).
>

> [!IMPORTANT]
> ASIM befindet sich derzeit in der Vorschauphase. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="common-asim-usage"></a>Allgemeine ASIM-Verwendung

Das Azure Sentinel-Informationsmodell (ASIM) bietet durch folgende Funktionen eine nahtlose Methode für die Verarbeitung verschiedener Quellen in einheitlichen, normalisierten Ansichten:

- **Quellenübergreifende Erkennungsfunktion**. Normalisierte Analyseregeln funktionieren quellenübergreifend, lokal und in der Cloud und erkennen Angriffe wie Brute-Force-Angriffe oder unmögliche Ortswechsel auf verschiedenen Systemen, einschließlich Okta, AWS und Azure.

- **Quellenunabhängige Inhalte**. Die Abdeckung sowohl vorgefertigter als auch benutzerdefinierter Inhalte mithilfe von ASIM wird automatisch auf alle Quellen erweitert, die ASIM unterstützen. Dies gilt selbst dann, wenn die Quelle erst nach dem Erstellen des Inhalts hinzugefügt wurde. Die Prozessereignisanalyse unterstützt beispielsweise alle Quellen, die ein Kunde zum Importieren der Daten verwenden kann, z. B. Microsoft Defender für Endpunkt, Windows-Ereignisse und Sysmon.

- **Unterstützung für benutzerdefinierte Quellen** in integrierten Analysen

- **Benutzerfreundlichkeit**. Nachdem ein Analyst ASIM eingeführt hat, ist das Schreiben von Abfragen viel einfacher, weil die Feldnamen immer identisch sind.

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM und die Metadaten von Open-Source-Sicherheitsereignissen

Das Azure Sentinel-Informationsmodell ist am Common Information Model [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) ausgerichtet und ermöglicht daher die vorhersagbare Korrelation von Entitäten aus mehreren normalisierten Tabellen.

OSSEM ist ein von der Community betriebenes Projekt, das hauptsächlich auf die Dokumentation und Standardisierung von Sicherheitsereignisprotokollen aus unterschiedlichen Datenquellen und Betriebssystemen ausgerichtet ist. Außerdem stellt das Projekt ein Common Information Model (CIM) bereit, das während der Datennormalisierungsprozeduren von Datentechnikern verwendet werden kann, damit Sicherheitsanalytiker Daten über verschiedene Datenquellen hinweg abfragen und analysieren können.

Weitere Informationen finden Sie in der [OSSEM-Referenzdokumentation](https://ossemproject.com/cdm/guidelines/entity_structure.html).

## <a name="asim-components"></a>ASIM-Komponenten

Die folgende Abbildung zeigt, wie nicht normalisierte Daten in normalisierte Inhalte umgewandelt und in Azure Sentinel verwendet werden können. Beispielsweise können Sie eine benutzerdefinierte, produktspezifische, nicht normalisierte Tabelle mithilfe eines Parsers und eines Normalisierungsschemas in normalisierte Daten konvertieren. Die normalisierten Daten können Sie in von Microsoft definierten sowie in benutzerdefinierten Analysen, Regeln, Arbeitsmappen, Abfragen usw. verwenden.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Umwandlung von nicht normalisierten in normalisierte Daten und Verwendung in Azure Sentinel":::

Das Azure Sentinel-Informationsmodell umfasst die folgenden Komponenten:

|Komponente  |BESCHREIBUNG  |
|---------|---------|
|**Normalisierte Schemas**     |   Standardsätze von vorhersagbaren Ereignistypen, die Sie beim Erstellen einheitlicher Funktionen verwenden können. <br><br>Jedes Schema definiert die Felder, die ein Ereignis, eine Namenskonvention für normalisierte Spalten und ein Standardformat für die Feldwerte darstellen. <br><br> In ASIM sind derzeit die folgenden Schemas definiert:<br> - [Netzwerksitzung](normalization-schema.md)<br> - [DNS-Aktivität](dns-normalization-schema.md)<br> - [Prozessereignis](process-events-normalization-schema.md)<br> - [Authentifizierungsereignis](authentication-normalization-schema.md)<br> - [Registrierungsereignis](registry-event-normalization-schema.md)<br> - [Dateiaktivität](file-event-normalization-schema.md)  <br><br>Weitere Informationen finden Sie unter [Schemas des Azure Sentinel-Informationsmodells](normalization-about-schemas.md).  |
|**Parser**     |  Zuordnung vorhandener Daten unter Verwendung von [KQL-Funktionen](/azure/data-explorer/kusto/query/functions/user-defined-functions) zu normalisierten Schemas. <br><br>Die von Microsoft entwickelten normalisierten Parser können über den GitHub-Ordner [Azure-Sentinel/Parsers](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers) bereitgestellt werden. Normalisierte Parser befinden sich in den Unterordnern, die mit **ASim*** beginnen.  <br><br>Weitere Informationen finden Sie unter [Parser des Azure Sentinel-Informationsmodells](normalization-about-parsers.md).     |
|**Inhalte für jedes normalisierte Schema**     |    Enthält Analyseregeln, Arbeitsmappen, Hunting-Abfragen und mehr. Die Inhalte für jedes normalisierte Schema können für alle normalisierten Daten verwendet werden, ohne dass quellenspezifische Inhalte erstellt werden müssen. <br><br>Weitere Informationen finden Sie unter [Inhalte des Azure Sentinel-Informationsmodells](normalization-content.md).   |
| | |

### <a name="asim-terminology"></a>ASIM-Terminologie

Im Azure Sentinel-Informationsmodell werden die folgenden Begriffe verwendet:

|Begriff  |BESCHREIBUNG  |
|---------|---------|
|**Meldendes Gerät**     |   Das System, das die Datensätze an Azure Sentinel sendet. Möglicherweise ist dieses System nicht das Zielsystem für den gesendeten Datensatz.      |
|**Datensatz**     |Eine Dateneinheit, die vom meldenden Gerät gesendet wird. Ein Datensatz wird häufig als `log`, `event` oder `alert` angegeben, kann sich aber auch auf andere Datentypen beziehen.         |
|**Inhalt** oder **Inhaltselement**     |Die verschiedenen, anpassbaren oder vom Benutzer erstellten Artefakte, die mit Azure Sentinel verwendet werden können. Zu diesen Artefakten gehören beispielsweise Analyseregeln, Hunting-Abfragen und Arbeitsmappen. Ein Inhaltselement ist ein solches Artefakt.|
| | |

<br>

## <a name="getting-started-with-asim"></a>Erste Schritte mit ASIM

So beginnen Sie mit der Verwendung von ASIM:

1. Stellen Sie die ASIM-Parser aus dem [Azure Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers) über die Ordner bereit, die mit `ASim*` beginnen.

1. Aktivieren Sie Analyseregelvorlagen, die ASIM verwenden. Weitere Informationen finden Sie unter [Inhaltsliste des Azure Sentinel-Informationsmodells (ASIM)](normalization-content.md#builtin).

1. Verwenden Sie ASIM anhand der folgenden Methoden in Ihrem Arbeitsbereich:

    - Verwenden Sie die ASIM-Hunting-Abfragen aus dem Azure Sentinel-GitHub-Repository, wenn Sie Protokolle in KQL auf der Azure Sentinel-Seite **Protokolle** abfragen. Weitere Informationen finden Sie unter [Inhaltsliste des Azure Sentinel-Informationsmodells (ASIM)](normalization-content.md#builtin).

    - Schreiben Sie mithilfe von ASIM Ihre eigenen Analyseregeln, oder [konvertieren Sie vorhandene](normalization-content.md#builtin).

    - Ermöglichen Sie die Verwendung integrierter Analysefunktionen für Ihre benutzerdefinierten Daten, indem Sie Parser für Ihre benutzerdefinierten Quellen [schreiben](normalization-about-parsers.md) und sie dem relevanten quellenunabhängigen Parser [hinzufügen](normalization-about-parsers.md#include).

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel gibt einen Überblick über die Normalisierung in Azure Sentinel und das Azure Sentinel-Informationsmodell.

Weitere Informationen finden Sie unter

- Sehen Sie sich das [ASIM-Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) an, oder befassen Sie sich mit den [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG).
- [Schemas des Azure Sentinel-Informationsmodells](normalization-about-schemas.md)
- [Parser des Azure Sentinel-Informationsmodells](normalization-about-parsers.md)
- [Inhalte des Azure Sentinel-Informationsmodells](normalization-content.md)
