---
title: Verwenden von SOC-ML-Anomalien zum Erkennen von Bedrohungen in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie die neuen SOC-ML-Anomalieerkennungsfunktionen in Azure Sentinel verwenden.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: daa21e4c0caf27f87f3143dacc1d22ac0a5c0f57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064048"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-azure-sentinel"></a>Verwenden von SOC-ML-Anomalien zum Erkennen von Bedrohungen in Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - SOC-ML-Anomalien befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="what-are-soc-ml-anomalies"></a>Was sind SOC-ML-Anomalien?

Da Angreifer und Verteidiger im Wettrüsten im Bereich der Cybersicherheit ständig um Vorteile kämpfen, finden Angreifer immer neue Wege, um der Erkennung zu entgehen. Allerdings führen Angriffe zwangsläufig immer noch zu einem ungewöhnlichen Verhalten in den angegriffenen Systemen. Die auf maschinellem Lernen basierenden SOC-ML-Anomalien von Azure Sentinel können dieses Verhalten mithilfe von sofort einsatzbereiten Analyseregelvorlagen erkennen. Anomalien weisen zwar nicht zwangsläufig auf schädliches oder sogar verdächtiges Verhalten hin, können aber verwendet werden, um die Erkennungen, Untersuchungen und Bedrohungssuche zu verbessern:

- **Zusätzliche Signale zur Verbesserung der Erkennung** Sicherheitsanalysten können Anomalien verwenden, um neue Bedrohungen zu erkennen und vorhandene Erkennungen effektiver zu gestalten. Eine einzelne Anomalie ist kein starkes Signal für schädliches Verhalten, aber in Kombination mit mehreren Anomalien an verschiedenen Punkten in der Kill Chain ist der kumulierte Effekt deutlich stärker. Sicherheitsanalysten können vorhandene Erkennungen auch verbessern, indem sie das durch Anomalien identifizierte ungewöhnliche Verhalten zu einer Bedingung für die Auslösung von Warnungen machen.

- **Beweise während der Untersuchungen**: Sicherheitsanalysten können Anomalien auch während der Untersuchungen verwenden, um eine Sicherheitsverletzung zu bestätigen, neue Untersuchungspfade zu finden und die potenziellen Auswirkungen zu bewerten. Diese Effizienz reduziert die Zeit, die Sicherheitsanalysten für Untersuchungen aufwenden.

- **Start der proaktiven Bedrohungssuche**: Bedrohungsspezialisten können Anomalien als Kontext verwenden, um festzustellen, ob bei ihren Abfragen verdächtiges Verhalten festgestellt wurde. Wenn das Verhalten verdächtig ist, weisen die Anomalien auch auf potenzielle Pfade für die weitere Suche hin. Diese von Anomalien bereitgestellten Hinweise reduzieren sowohl die Zeit zum Erkennen einer Bedrohung als auch die Wahrscheinlichkeit, dass diese Schaden verursacht.

Anomalien können leistungsstarke Tools sein, verursachen aber bekanntermaßen stark abweichende Ergebnisse. Sie erfordern in der Regel eine aufwändige Optimierung für bestimmte Umgebungen oder komplexe Nachbearbeitung. Azure Sentinel SOC-ML-Anomalievorlagen werden von unserem Data Science-Team optimiert, um einen standardmäßigen Nutzen zu bieten. Wenn jedoch weitere Optimierungen erforderlich sind, ist das Verfahren dazu einfach und erfordert keine Kenntnisse des maschinellen Lernens. Die Schwellenwerte und Parameter für viele der Anomalien können über die bereits vertraute Benutzeroberfläche für Analyseregel konfiguriert und optimiert werden. Die Leistung der ursprünglichen Schwellenwerte und Parameter kann mit der Leistung der neuen Schwellenwerte und Parameter innerhalb der Schnittstelle verglichen und bei Bedarf während einer Test- oder Flightingphase weiter optimiert werden. Sobald die Anomalie die Leistungsziele erfüllt, kann sie mit dem neuen Schwellenwert oder den neuen Parametern per Mausklick in die Produktionsumgebung übernommen werden. Mit Azure Sentinel SOC-ML-Anomalien können Sie die Vorteile von Anomalien ohne großen Aufwand nutzen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie mit SOC-ML Anomalien in Azure Sentinel erkennen können.

- Erfahren Sie, wie Sie [Anomalieregeln anzeigen, erstellen, verwalten und optimieren](work-with-anomaly-rules.md).
- Erfahren Sie mehr über [andere Arten von Analyseregeln](detect-threats-built-in.md).
