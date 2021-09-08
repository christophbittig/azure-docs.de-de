---
title: Diensteinschränkungen
titleSuffix: Azure Digital Twins
description: Diagramm mit den Diensteinschränkungen von Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: cb86fe0d4a99897821b389682c5a131ce0b9118c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472873"
---
# <a name="azure-digital-twins-service-limits"></a>Einschränkungen des Azure Digital Twins-Diensts

In den folgenden Abschnitten werden die Dienstgrenzwerte von Azure Digital Twins beschrieben.

> [!NOTE]
> In einigen Bereichen dieses Diensts lassen sich Grenzwerte anpassen. Diese sind in der nachfolgenden Tabelle in der Spalte *Anpassbar?* entsprechend gekennzeichnet. Wenn der Grenzwert angepasst werden kann, enthält die Spalte *Anpassbar?* den Wert *Ja*.
>
> Falls Ihr Unternehmen die Anhebung eines anpassbaren Grenzwertes oder Kontingents über den Standardgrenzwert hinaus benötigt, können Sie zusätzliche Ressourcen anfordern, indem Sie [ein Supportticket eröffnen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-by-type"></a>Grenzwerte nach Typ

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Arbeiten mit Grenzwerten

Wenn ein Grenzwert erreicht wird, drosselt der Dienst darüber hinausgehende Anforderungen, sodass bei diesen Anforderungen die Fehlerantwort 429 ausgelöst wird.

Um dies zu verwalten, finden Sie hier einige Empfehlungen für die Arbeit mit Grenzwerten.
* **Verwenden Sie Wiederholungslogik.** Die [Azure Digital Twins SDKs](concepts-apis-sdks.md) implementieren Wiederholungslogik für fehlerhafte Anforderungen. Wenn Sie also mit einem bereitgestellten SDK arbeiten, ist diese Funktion bereits integriert. Andernfalls sollten Sie die Wiederholungslogik in Ihrer eigenen Anwendung implementieren. Der Dienst sendet einen `Retry-After`-Header in der Fehlerantwort zurück, den Sie verwenden können, um zu bestimmen, wie lange vor der Wiederholung gewartet werden soll.
* **Verwenden Sie Schwellenwerte und Benachrichtigungen, um vor dem Erreichen von Grenzwerten zu warnen.** Einige der Dienstgrenzwerte für Azure Digital Twins verfügen über entsprechende [Metriken](troubleshoot-metrics.md), die zum Nachverfolgen der Nutzung in diesen Bereichen verwendet werden können. Informationen zum Konfigurieren von Schwellenwerten und Einrichten einer Warnung für eine Metrik, wenn ein Schwellenwert erreicht wird, finden Sie in den Anweisungen unter [Problembehandlung: Einrichten von Warnungen](troubleshoot-alerts.md). Um Benachrichtigungen für andere Grenzwerte einzurichten, bei denen keine Metriken bereitgestellt werden, sollten Sie diese Logik in Ihren eigenen Anwendungscode implementieren.
* **Bereitstellung in großem Umfang über mehrere Instanzen hinweg.** Vermeiden Sie einen Single Point of Failure. Statt eines großen Diagramms für Ihre gesamte Bereitstellung sollten Sie erwägen, Teilmengen von Zwillingen logisch (z. B. nach Region oder Mandant) über mehrere Instanzen hinweg zu bereitzustellen. 

## <a name="next-steps"></a>Nächste Schritte

In der Übersicht über den Dienst erfahren Sie mehr über die aktuelle Version von Azure Digital Twins:
* [Was ist Azure Digital Twins?](overview.md)
