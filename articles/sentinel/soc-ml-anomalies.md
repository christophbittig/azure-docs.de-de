---
title: Verwenden von SOC-ML-Anomalien zur Erkennung von Bedrohungen in Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie die neuen SOC-ML-Anomalieerkennungsfunktionen in Microsoft Sentinel verwenden.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 233f83083a061bf12caae58172d2c80bab876fcf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519836"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-microsoft-sentinel"></a>Verwenden von SOC-ML-Anomalien zur Erkennung von Bedrohungen in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - SOC-ML-Anomalien befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="what-are-soc-ml-anomalies"></a>Was sind SOC-ML-Anomalien?

Da Angreifer und Verteidiger im Wettrüsten im Bereich der Cybersicherheit ständig um Vorteile kämpfen, finden Angreifer immer neue Wege, um der Erkennung zu entgehen. Allerdings führen Angriffe zwangsläufig immer noch zu einem ungewöhnlichen Verhalten in den angegriffenen Systemen. Die auf maschinellem Lernen basierenden SOC-ML-Anomalien von Microsoft Sentinel können dieses Verhalten mithilfe von sofort einsatzbereiten Analyseregelvorlagen erkennen. Anomalien weisen zwar nicht zwangsläufig auf schädliches oder sogar verdächtiges Verhalten hin, können aber verwendet werden, um die Erkennungen, Untersuchungen und Bedrohungssuche zu verbessern:

- **Zusätzliche Signale zur Verbesserung der Erkennung** Sicherheitsanalysten können Anomalien verwenden, um neue Bedrohungen zu erkennen und vorhandene Erkennungen effektiver zu gestalten. Eine einzelne Anomalie ist kein starkes Signal für schädliches Verhalten, aber in Kombination mit mehreren Anomalien an verschiedenen Punkten in der Kill Chain ist der kumulierte Effekt deutlich stärker. Sicherheitsanalysten können vorhandene Erkennungen auch verbessern, indem sie das durch Anomalien identifizierte ungewöhnliche Verhalten zu einer Bedingung für die Auslösung von Warnungen machen.

- **Beweise während der Untersuchungen**: Sicherheitsanalysten können Anomalien auch während der Untersuchungen verwenden, um eine Sicherheitsverletzung zu bestätigen, neue Untersuchungspfade zu finden und die potenziellen Auswirkungen zu bewerten. Diese Effizienz reduziert die Zeit, die Sicherheitsanalysten für Untersuchungen aufwenden.

- **Start der proaktiven Bedrohungssuche**: Bedrohungsspezialisten können Anomalien als Kontext verwenden, um festzustellen, ob bei ihren Abfragen verdächtiges Verhalten festgestellt wurde. Wenn das Verhalten verdächtig ist, weisen die Anomalien auch auf potenzielle Pfade für die weitere Suche hin. Diese von Anomalien bereitgestellten Hinweise reduzieren sowohl die Zeit zum Erkennen einer Bedrohung als auch die Wahrscheinlichkeit, dass diese Schaden verursacht.

Anomalien können leistungsstarke Tools sein, verursachen aber bekanntermaßen stark abweichende Ergebnisse. Sie erfordern in der Regel eine aufwändige Optimierung für bestimmte Umgebungen oder komplexe Nachbearbeitung. Das Data-Science-Team von Microsoft optimiert die SOC-ML-Anomalievorlagen von Microsoft Sentinel so, dass sie sofort einen Mehrwert bieten. Wenn weitere Optimierungen erforderlich sind, können diese jedoch leicht und ohne Vorkenntnisse im Bereich maschinelles Lernen vorgenommen werden. Die Schwellenwerte und Parameter für viele der Anomalien können über die bereits vertraute Benutzeroberfläche für Analyseregel konfiguriert und optimiert werden. Die Leistung der ursprünglichen Schwellenwerte und Parameter kann mit der Leistung der neuen Schwellenwerte und Parameter innerhalb der Schnittstelle verglichen und bei Bedarf während einer Test- oder Flightingphase weiter optimiert werden. Sobald die Anomalie die Leistungsziele erfüllt, kann sie mit dem neuen Schwellenwert oder den neuen Parametern per Mausklick in die Produktionsumgebung übernommen werden. Mit den SOC-ML-Anomalien von Microsoft Sentinel können Sie die Vorteile einer Anomalieerkennung ohne großen Aufwand nutzen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mit SOC-ML Anomalien in Microsoft Sentinel erkennen können.

- Erfahren Sie, wie Sie [Anomalieregeln anzeigen, erstellen, verwalten und optimieren](work-with-anomaly-rules.md).
- Erfahren Sie mehr über [andere Arten von Analyseregeln](detect-threats-built-in.md).
