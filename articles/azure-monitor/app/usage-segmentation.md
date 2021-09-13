---
title: Benutzer-, Sitzungs- und Ereignisanalyse in Application Insights
description: Demografische Analyse der Benutzer Ihrer Web-App.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 09dc9ba915b0ecf99219aadd9214192b9f4b1e19
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347206"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Benutzer-, Sitzungs- und Ereignisanalyse in Azure Application Insights

Ermitteln Sie, wann Personen Ihre Web-App verwenden, für welche Seiten sie sich am meisten interessieren, wo sich die Benutzer befinden, und welche Browser und Betriebssysteme sie verwenden. Analysieren Sie Geschäfts- und Nutzungstelemetriedaten mithilfe von [Application Insights](./app-insights-overview.md).

:::image type="content" source="./media/usage-segmentation/users.png" alt-text="Die Bildschirmaufnahme zeigt die Registerkarte „Benutzer“ mit einem Flächendiagramm." lightbox="./media/usage-overview/users.png":::

## <a name="get-started"></a>Erste Schritte

Wenn Sie im Application Insights-Portal auf den Blättern „Benutzer“, „Sitzungen“ und „Ereignissen“ noch keine Daten sehen, [erfahren Sie hier mehr zum Einstieg in die Nutzungstools](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Das Segmentierungstool für Benutzer, Sitzungen und Ereignisse

Drei der Nutzungsblätter verwenden dasselbe Tool zum Aufteilen von Telemetriedaten aus Ihrer Web-App aus drei Perspektiven. Durch Filtern und Aufteilen der Daten gewinnen Sie Einblicke in die relative Nutzung verschiedener Seiten und Funktionen.

* **Benutzertool:** Wie viele Personen haben Ihre App und zugehörige Funktionen verwendet?  Benutzer werden mithilfe von anonymen, in Browsercookies gespeicherten IDs gezählt. Eine einzelne Person, die verschiedene Browser oder Computer benutzt, wird als mehrere Benutzer gezählt.
* **Sitzungstool:** Bei wie vielen Benutzeraktivitätssitzungen wurden bestimmte Seiten und Funktionen der App verwendet? Eine Sitzung wird nach einer halben Stunde der Benutzerinaktivität oder nach 24 Stunden ununterbrochener Nutzung gezählt.
* **Ereignistool:** Wie oft werden bestimmte Seiten und Funktionen der App verwendet? Eine Seitenansicht wird gezählt, wenn eine Seite Ihrer App in einem Browser geladen wird, vorausgesetzt, Sie haben sie [instrumentiert](./javascript.md). 

    Ein benutzerdefiniertes Ereignis stellt eine Aktion in Ihrer App dar, häufig eine Benutzerinteraktion wie die Auswahl einer Schaltfläche oder den Abschluss einer Aufgabe. Sie fügen Code in Ihre App ein, um [benutzerdefinierte Ereignisse zu generieren](./api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Abfragen für bestimmte Benutzer

Untersuchen Sie verschiedene Benutzergruppen, indem Sie die Abfrageoptionen oben im Benutzertools anpassen:

- Während: Wählen Sie einen Zeitbereich aus.
- Anzeigen: Wählen Sie eine Kohorte von Benutzern für die Analyse aus.
- Verwendet von: Wählen Sie benutzerdefinierte Ereignisse, Anforderungen und Seitenaufrufe.
- Ereignisse: Wählen Sie mehrere Ereignisse, Anforderungen und Seitenaufrufe aus, die Benutzer anzeigen, die mindestens eine, aber nicht unbedingt alle der ausgewählten Aktionen durchgeführt haben.
- Nach Wert auf der X-Achse: Wählen Sie, wie Datenbuckets erstellt werden, entweder anhand einer Zeitspanne oder einer anderen Eigenschaft, z.B. Browser oder Ort.
- Aufteilen nach: Wählen Sie eine Eigenschaft aus, anhand derer die Daten aufgeteilt oder segmentiert werden. 
- Filter hinzufügen: Beschränken Sie die Abfrage auf bestimmte Benutzer, Sitzungen oder Ereignisse basierend auf deren Eigenschaften, z.B. Browser oder Ort. 
 
## <a name="meet-your-users"></a>Informationen zu Benutzern

Der Abschnitt **Meet your users** (Informationen zu Benutzern) enthält Informationen zu fünf Beispielbenutzern, die der aktuellen Abfrage entsprechen. Das Untersuchen des Verhaltens von einzelnen Personen sowie von Aggregaten kann Erkenntnisse über die tatsächliche Verwendung Ihrer App durch Benutzer bieten.

## <a name="next-steps"></a>Nächste Schritte

- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](./api-custom-events-metrics.md#trackevent) oder [Seitenansichten](./api-custom-events-metrics.md#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Trichter](usage-funnels.md)
    - [Vermerkdauer](usage-retention.md)
    - [Benutzerabläufe](usage-flows.md)
    - [Arbeitsmappen](../visualize/workbooks-overview.md)
    - [Hinzufügen von Benutzerkontext](./usage-overview.md)