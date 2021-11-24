---
title: Schnelle Erkennung von Bedrohungen mit NRT-Analyseregeln (Near-Real-Time) in Microsoft Sentinel | Microsoft Dokumentation
description: In diesem Artikel wird erläutert, wie Sie mit den neuen NRT-Analyseregeln (Near-Real-Time) in Microsoft Sentinel Bedrohungen schnell erkennen können.
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
ms.openlocfilehash: 1b46989647146d433f86b7e9698222900fb455dd
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518848"
---
# <a name="detect-threats-quickly-with-near-real-time-nrt-analytics-rules-in-microsoft-sentinel"></a>Schnelle Erkennung von Bedrohungen mit NRT-Analyseregeln (Near-Real-Time) in Microsoft Sentinel

> [!IMPORTANT]
>
> - NRT-Regeln (nahezu in Echtzeit) befinden sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="what-are-near-real-time-nrt-analytics-rules"></a>Was sind Analyseregeln nahezu in Echtzeit (Near-Real-Time, NRT)?

Wenn Sie mit Sicherheitsbedrohungen konfrontiert sind, sind Zeit und Geschwindigkeit von entscheidender Bedeutung. Sie müssen sich der Bedrohungen bewusst sein, sobald sie auftreten, sodass Sie sie analysieren und schnell reagieren können, um sie abzuwehren. Die Near-Real-Time (NRT)-Analyseregeln von Microsoft Sentinel bieten Ihnen eine schnellere Erkennung von Bedrohungen - näher an der eines SIEM vor Ort - und die Möglichkeit, die Reaktionszeiten in bestimmten Szenarien zu verkürzen.

Microsoft Sentinels [Nah-Echtzeit-Analyseregeln](detect-threats-built-in.md#nrt) sorgen für eine minutengenaue Erkennung von Bedrohungen - sofort nach der Installation. Diese Art von Regel wurde so konzipiert, dass sie extrem reaktionsschnell ist, indem sie ihre Abfrage in Abständen von nur einer Minute durchführt.

## <a name="how-do-they-work"></a>Wie funktionieren sie?

NRT-Regeln sind hartcodiert, sodass sie einmal pro Minute ausgeführt werden und die in der vorangegangenen Minute erfassten Ereignisse aufzeichnen, damit sie Ihnen möglichst aktuelle Informationen liefern können.

Im Gegensatz zu regulären geplanten Regeln, die mit einer integrierten fünfminütigen Verzögerung ausgeführt werden, um die Zeitverzögerung bei der Erfassung zu berücksichtigen, werden NRT-Regeln mit nur einer zweiminütigen Verzögerung ausgeführt und lösen das Problem der Erfassungsverzögerung, indem sie die Erfassungszeit von Ereignissen anstelle der Zeit ihrer Generierung an der Quelle (Feld „TimeGenerated“) abfragen. Dadurch verbessern sich sowohl die Häufigkeit als auch die Genauigkeit Ihrer Erkennungen. (Um dieses Problem besser zu verstehen, siehe [Abfrageplanung und Warnschwellen](detect-threats-custom.md#query-scheduling-and-alert-threshold) und [Verwaltung der Aufnahmeverzögerung in geplanten Analyseregeln](ingestion-delay.md)).

NRT-Regeln verfügen über viele derselben Features und Funktionen wie geplante Analyseregeln. Es stehen alle Funktionen zur Warnungsanreicherung zur Verfügung – Sie können Entitäten zuordnen und benutzerdefinierte Details anzeigen sowie dynamische Inhalte für Warnungsdetails konfigurieren. Sie können auswählen, wie Warnungen in Vorfälle gruppiert werden. Sie können die Ausführung einer Abfrage vorübergehend unterdrücken, nachdem sie ein Ergebnis generiert hat, und Sie können Automatisierungsregeln sowie Playbooks definieren, die als Reaktion auf Warnungen und Vorfälle, die von der Regel generiert werden, ausgeführt werden.

Im Moment haben diese Vorlagen nur eine begrenzte Anwendung, wie unten beschrieben, aber die Technologie entwickelt sich schnell weiter und wächst.

## <a name="considerations"></a>Überlegungen
Die folgenden Einschränkungen gelten derzeit für die Verwendung der NRT-Regeln:

1. Derzeit können nicht mehr als 20 Regeln pro Kunde definiert werden.

1. Da diese Art von Regeln neu ist, ist ihre Syntax derzeit noch begrenzt, wird sich aber nach und nach weiterentwickeln. Daher gelten derzeit die folgenden Einschränkungen:

    1. Die in einer NRT-Regel definierte Abfrage kann nur auf **eine Tabelle** verweisen. Abfragen können sich jedoch auf mehrere Watchlists und Threat Intelligence-Feeds beziehen.

    1. Sie können keine Unions oder Joins verwenden.

    1. Da dieser Regeltyp nahezu in Echtzeit arbeitet, haben wir die integrierte Verzögerung auf ein Minimum (zwei Minuten) reduziert.

    1. Da die NRT-Regeln die Erfassungszeit und nicht die Ereignisgenerierungszeit (dargestellt durch das Feld „TimeGenerated“) verwenden, können Sie die Verzögerung der Datenquelle und die Wartezeit bei der Erfassung (siehe oben) getrost ignorieren.

    1. Abfragen können nur innerhalb eines einzelnen Arbeitsbereichs ausgeführt werden. Es gibt keine arbeitsbereichsübergreifende Funktion.

    1. Es gibt keine Ereignisgruppierung. NRT-Regeln erzeugen eine einzelne Warnung, die alle anwendbaren Ereignisse gruppiert.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie die Regeln für Near-Real-Time-Analysen (NRT) in Microsoft Sentinel funktionieren.

- Erfahren Sie, wie Sie [NRT-Regeln erstellen](create-nrt-rules.md).
- Erfahren Sie mehr über [andere Arten von Analyseregeln](detect-threats-built-in.md).
