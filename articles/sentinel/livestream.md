---
title: Verwenden des Hunting-Livestreams in Microsoft Sentinel zum Erkennen von Bedrohungen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie der Hunting-Livestream in Microsoft Sentinel verwendet werden kann, um den Überblick über Daten zu behalten.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 22e3eafbbae9259ddea56e9927055da08236eedb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520083"
---
# <a name="use-hunting-livestream-in-microsoft-sentinel-to-detect-threats"></a>Verwenden des Hunting-Livestreams in Microsoft Sentinel zum Erkennen von Bedrohungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Die ressourcenübergreifenden Abfragefunktionen (siehe im Folgenden erläuterte Elemente in Fettschrift) befinden sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

Verwenden Sie den Hunting-Livestream, um interaktive Sitzungen zu erstellen, mit denen Sie neu erstellte Abfragen beim Eintreten von Ereignissen testen, Benachrichtigungen von den Sitzungen bei einer Übereinstimmung erhalten und bei Bedarf Untersuchungen starten können. Sie können schnell mithilfe einer beliebigen Log Analytics-Abfrage eine Livestreamsitzung erstellen.

- **Testen neu erstellter Abfragen, wenn Ereignisse auftreten**
    
    Sie können Abfragen ohne Konflikte mit aktuellen Regeln testen und anpassen, die aktiv auf Ereignisse angewendet werden. Nachdem Sie bestätigt haben, dass diese neuen Abfragen erwartungsgemäß funktionieren, ist es einfach, sie zu benutzerdefinierten Warnungsregeln höher zu stufen, indem Sie eine Option auswählen, die die Sitzung zu einer Warnung erhöht.

- **Benachrichtigung beim Auftreten von Bedrohungen erhalten**
    
    Sie können Bedrohungsdatenfeeds mit aggregierten Protokolldaten vergleichen und eine Benachrichtigung erhalten, wenn eine Entsprechung auftritt. Bedrohungsdatenfeeds sind fortlaufende Datenströme, die sich auf potenzielle oder aktuelle Bedrohungen beziehen, sodass die Benachrichtigung möglicherweise auf eine potenzielle Bedrohung für Ihre Organisation hinweist. Erstellen Sie eine Livestreamsitzung anstelle einer benutzerdefinierten Warnungsregel, wenn Sie über ein mögliches Problem benachrichtigt werden möchten, ohne dass der Mehraufwand für die Verwaltung einer benutzerdefinierten Warnungsregel anfällt.

- **Starten von Untersuchungen**
    
    Wenn eine aktive Untersuchung durchgeführt wird, die eine Ressource wie einen Host oder einen Benutzer beinhaltet, können Sie bestimmte (oder beliebige) Aktivitäten in den Protokolldaten anzeigen, während diese Aktivitäten für die Ressource auftreten. Sie können eine Benachrichtigung erhalten, wenn diese Aktivität auftritt.


## <a name="create-a-livestream-session"></a>Erstellen einer Livestreamsitzung

Sie können eine Livestreamsitzung aus einer vorhandenen Hunting-Abfrage erstellen oder eine Sitzung von Grund auf neu erstellen.

1. Navigieren Sie im Azure-Portal zu **Sentinel** > **Bedrohungsverwaltung** > **Hunting**.

1. So erstellen Sie eine Livestreamsitzung aus einer Hunting-Abfrage
    
    1. Ermitteln Sie auf der Registerkarte **Abfragen** die zu verwendende Hunting-Abfrage.
    1. Klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Add to livestream** (Zu Livestream hinzufügen) aus. Beispiel:
    
    > [!div class="mx-imgBorder"]
    > ![Erstellen einer Livestreamsitzung aus einer Microsoft Sentinel-Hunting-Abfrage](./media/livestream/livestream-from-query.png)

1. So erstellen Sie eine Livestreamsitzung von Grund auf neu 
    
    1. Wählen Sie die Registerkarte **Livestream** aus.
    1. Klicken Sie auf **+ Neuer Livestream**.
    
1. Im Bereich **Livestream**:
    
    - Wenn Sie den Livestream aus einer Abfrage gestartet haben, überprüfen Sie die Abfrage, und nehmen Sie die gewünschten Änderungen vor.
    - Wenn Sie den Livestream von Grund auf neu gestartet haben, erstellen Sie Ihre Abfrage.

    > [!NOTE]
    > Der Livestream unterstützt **ressourcenübergreifende Abfragen** (in der Vorschauphase) von Daten in Azure Data Explorer. Lesen Sie den Artikel zu [**übergreifenden Abfragen von Log Analytics- oder Application Insights-Ressourcen und Azure Data Explorer**](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).

1. Wählen Sie in der Befehlsleiste **Wiedergeben** aus.
    
    Die Statusleiste unter der Befehlsleiste gibt an, ob die Livestreamsitzung ausgeführt wird oder angehalten ist. Im folgenden Beispiel wird die Sitzung ausgeführt:
    
    > [!div class="mx-imgBorder"]
    > ![Erstellen einer Livestreamsitzung aus Azure Sentinel-Hunting](./media/livestream/livestream-session.png)

1. Wählen Sie in der Befehlsleiste **Speichern** aus.
    
    Wenn Sie nicht **Anhalten** auswählen, wird die Sitzung fortgesetzt, bis Sie sich vom Azure-Portal abgemeldet haben.

## <a name="view-your-livestream-sessions"></a>Anzeigen von Livestreamsitzungen

1. Navigieren Sie im Azure-Portal zur Registerkarte **Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Livestream**.

1. Wählen Sie die Livestreamsitzung aus, die Sie anzeigen oder bearbeiten möchten. Beispiel:
    
    > [!div class="mx-imgBorder"]
    > ![Erstellen einer Livestreamsitzung aus einer Microsoft Sentinel-Hunting-Abfrage](./media/livestream/livestream-tab.png)
    
    Ihre ausgewählte Livestreamsitzung wird geöffnet, damit Sie sie wiedergeben, anhalten, bearbeiten usw. können.

## <a name="receive-notifications-when-new-events-occur"></a>Empfangen von Benachrichtigungen, wenn neue Ereignisse auftreten

Da für Livestreambenachrichtigungen für neue Ereignisse Benachrichtigungen des Azure-Portals verwendet werden, erhalten Sie diese immer, wenn Sie das Azure-Portal verwenden. Beispiel:

![Benachrichtigung des Azure-Portals für Livestreams](./media/livestream/notification.png)

Wählen Sie die Benachrichtigung aus, um den Bereich **Livestream** zu öffnen.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Erhöhen einer Livestreamsitzung zu einer Warnung

Sie können eine Livestreamsitzung in eine neue Warnung höher stufen, indem Sie auf der Befehlsleiste der relevanten Livestreamsitzung **Auf Warnung erhöhen** auswählen:

> [!div class="mx-imgBorder"]
> ![Höherstufen einer Livestreamsitzung in eine Warnung](./media/livestream/elevate-to-alert.png)

Mit dieser Aktion wird der Regelerstellungs-Assistent geöffnet, der mit der Abfrage, die der Livestreamsitzung zugeordnet ist, bereits aufgefüllt ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie der Hunting-Livestream in Microsoft Sentinel verwendet wird. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)
