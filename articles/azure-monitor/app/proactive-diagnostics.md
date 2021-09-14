---
title: Intelligente Erkennung in Azure Application Insights | Microsoft Docs
description: Application Insights führt eine automatische umfassende Analyse Ihrer App-Telemetrie durch und warnt Sie vor potenziellen Problemen.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 79fe723a7972d265621a1a52ea589bf7dcf6ec62
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536734"
---
# <a name="smart-detection-in-application-insights"></a>Intelligente Erkennung in Application Insights

>[!NOTE]
>Sie können die intelligente Erkennung in Ihrer Application Insights-Ressource zu einer warnungsbasierten Erkennung migrieren. Bei der Migration werden Warnungsregeln für die verschiedenen Module der intelligenten Erkennung erstellt. Nach der Erstellung können Sie diese Regeln wie alle anderen Azure Monitor-Warnungsregeln verwalten und konfigurieren. Sie können auch Aktionsgruppen für diese Regeln konfigurieren, was mehrere Methoden für das Ausführen von Aktionen oder für das Auslösen von Benachrichtigungen bei neuen Erkennungen ermöglicht.
>
> Weitere Informationen finden Sie unter [Migration zu intelligenten Erkennungswarnungen](../alerts/alerts-smart-detections-migration.md).

Die intelligente Erkennung warnt Sie automatisch vor potenziellen Leistungsproblemen und Fehleranomalien in Ihrer Webanwendung. Sie führt eine proaktive Analyse der Telemetriedaten durch, die Ihre App an [Application Insights](./app-insights-overview.md) sendet. Bei einem plötzlichen Anstieg der Fehlerraten oder bei ungewöhnlichen Mustern in der Client- oder Serverleistung erhalten Sie eine Warnung. Diese Funktion muss nicht konfiguriert werden. Sie wird ausgeführt, wenn Ihre Anwendung genügend Telemetriedaten sendet.

Sie können über die erhaltenen E-Mails und das Blatt für die intelligenten Erkennung auf Ergebnisse der intelligenten Erkennung zugreifen.

## <a name="review-your-smart-detections"></a>Überprüfen der Ergebnisse der intelligenten Erkennung
Sie haben zwei Möglichkeiten, Erkennungen anzuzeigen:

* **Sie erhalten eine E-Mail** von Application Insights. Hier sehen Sie ein typisches Beispiel:
  
    ![E-Mail-Warnung](./media/proactive-diagnostics/03.png)
  
    Klicken Sie auf die große Schaltfläche, um ausführlichere Informationen im Portal zu öffnen.
* **Das Blatt „Intelligente Erkennung“** in Application Insights Wählen Sie **Intelligente Erkennung** im Menü **Untersuchen** aus, um eine Liste der aktuellen Erkennungen anzuzeigen.

![Aktuelle Erkennungen anzeigen](./media/proactive-diagnostics/04.png)

Wählen Sie eine Erkennung aus, um ihre Details anzuzeigen.

## <a name="what-problems-are-detected"></a>Welche Probleme werden erkannt?

Die intelligente Erkennung entdeckt eine Reihe von Problemen und gibt Benachrichtigungen dazu aus, z. B.:

* [Intelligente Erkennung – Fehlerabweichungen](./proactive-failure-diagnostics.md). Wir nutzen maschinelles Lernen, um die voraussichtliche Rate fehlerhafter Anforderungen für Ihre App (korreliert mit Lastangaben und anderen Faktoren) festzulegen. Sendet eine Benachrichtigung, falls die Fehlerrate den erwarteten Rahmen überschreitet.
* [Intelligente Erkennung – Leistungsabweichungen](./proactive-performance-diagnostics.md). Sendet eine Benachrichtigung, wenn sich die Antwortzeit eines Vorgangs oder einer Abhängigkeitsdauer im Vergleich zur historischen Baseline verlangsamt. Sendet außerdem eine Benachrichtigung, wenn wir ein anomales Muster bei der Antwortzeit oder Seitenladezeit feststellen.   
* Allgemeine Beeinträchtigungen und Probleme wie [Verschlechterung der Ablaufverfolgung](./proactive-trace-severity.md), [Speicherverlust](./proactive-potential-memory-leak.md), [anomaler Anstieg im Ausnahmevolume](./proactive-exception-volume.md) und [Verdachtsmomente für ein Sicherheitsproblem](./proactive-application-security-detection-pack.md).

(Über die Hilfelinks in den jeweiligen Benachrichtigungen gelangen Sie zu den relevanten Artikeln.)

## <a name="smart-detection-email-notifications"></a>E-Mail-Benachrichtigungen bei intelligenter Erkennung

Alle Regeln für die intelligente Erkennung, mit Ausnahme der als _Vorschauversion_ gekennzeichneten Regeln, sind standardmäßig so konfiguriert, dass bei Erkennungen E-Mail-Benachrichtigungen gesendet werden.

Zum Konfigurieren von E-Mail-Benachrichtigungen für eine bestimmte Regel für die intelligente Erkennung können Sie das Blatt **Einstellungen** für die intelligente Erkennung öffnen, und die entsprechende Regel auswählen. Dadurch wird das Blatt **Regel bearbeiten** geöffnet.

Alternativ können Sie die Konfiguration mithilfe von Azure Resource Manager-Vorlagen ändern. Weitere Informationen finden Sie unter [Verwalten von intelligenten Erkennungsregeln von Azure Application Insights mit Azure Resource Manager-Vorlagen](./proactive-arm-config.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]



## <a name="next-steps"></a>Nächste Schritte
Mit den folgenden Diagnosetools können Sie die Telemetrie Ihrer App untersuchen:

* [Metrik-Explorer](../essentials/metrics-charts.md)
* [Suchexplorer](./diagnostic-search.md)
* [Analytics: Leistungsfähige Abfragesprache](../logs/log-analytics-tutorial.md)

Die intelligente Erkennung erfolgt automatisch. Vielleicht möchten Sie aber weitere Warnungen einrichten?

* [Einrichten von Warnungen in Application Insights](../alerts/alerts-log.md)
* [Verfügbarkeitswebtests](./monitor-web-app-availability.md)
