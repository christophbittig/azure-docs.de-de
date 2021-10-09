---
title: Azure Key Vault-Warnungen
description: Erstellen Sie ein Dashboard zum Überwachen der Integrität Ihres Schlüsseltresors, und konfigurieren Sie Warnungen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 4c1f63bdc13822b7eb48dc5410a990dc75f3453e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060780"
---
# <a name="alerting-for-azure-key-vault"></a>Warnmeldungen für Azure Key Vault

## <a name="overview"></a>Übersicht

Sobald Sie damit begonnen haben, Ihre Produktionsgeheimnisse im Schlüsseltresor zu speichern, sollten Sie durch eine Überwachung der Integrität Ihres Schlüsseltresors sicherstellen, dass Ihr Dienst wie vorgesehen funktioniert. Wenn Sie Ihren Dienst skalieren, steigt die Anzahl von Anforderungen, die an Ihren Schlüsseltresor gesendet werden. Dies kann die Latenzzeit Ihrer Anforderungen erhöhen und in extremen Fällen zu einer Drosselung Ihrer Anforderungen führen – was sich auf die Leistung Ihres Diensts auswirkt. Sie müssen außerdem benachrichtigt werden, wenn Ihr Schlüsseltresor eine ungewöhnliche Anzahl von Fehlercodes sendet, damit Sie bei Problemen mit Zugriffsrichtlinien oder Firewallkonfiguration schnell handeln können. In diesem Dokument werden die folgenden Themen behandelt:

+ Grundlegende Key Vault-Metriken für die Überwachung
+ Konfigurieren von Metriken und Erstellen eines Dashboards
+ Erstellen von Warnungen bei angegebenen Schwellenwerten

Von Azure Monitor für Key Vault werden Protokolle mit Metriken kombiniert, um eine globale Überwachungslösung bereitzustellen. [Erfahren Sie hier mehr über Azure Monitor für den Schlüsseltresor](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Konfigurieren von Warnungen für Ihren Schlüsseltresor 

In diesem Abschnitt wird gezeigt, wie Sie Warnungen für Ihren Schlüsseltresor konfigurieren, damit Ihr Team sofort benachrichtigt wird, wenn sich Ihr Schlüsseltresor in einem fehlerhaften Zustand befindet. Sie können Warnungen konfigurieren, mit denen eine E-Mail gesendet (vorzugsweise an eine Verteilerliste für ein Team), eine Event Grid-Benachrichtigung ausgelöst, eine Telefonnummer angerufen oder eine SMS gesendet wird. Es ist auch möglich, statische Warnungen basierend auf einem festen Wert auszuwählen oder dynamische Warnungen zu konfigurieren, die Sie benachrichtigen, wenn für eine überwachte Metrik der Durchschnittsgrenzwert für Ihren Schlüsseltresor innerhalb eines definierten Zeitraums mit einer festgelegten Häufigkeit überschritten wird. 

> [!IMPORTANT]
> Beachten Sie, dass es bis zu 10 Minuten dauern kann, bis neu konfigurierte Warnungen damit beginnen, Benachrichtigungen zu versenden. 

### <a name="configure-an-action-group"></a>Konfigurieren einer Aktionsgruppe 

Eine Aktionsgruppe ist eine konfigurierbare Liste mit Benachrichtigungen und Eigenschaften.

1. Melden Sie sich beim Azure-Portal an.
2. Suchen Sie im Suchfeld nach **Warnungen**.
3. Wählen Sie **Aktionen verwalten** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Schaltfläche „Aktionen verwalten“](../media/alert-6.png)

4. Klicken Sie auf **+ Aktionsgruppe hinzufügen**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Schaltfläche „+ Aktionsgruppe hinzufügen“](../media/alert-7.png)

5. Wählen Sie den **Aktionstyp** für Ihre Aktionsgruppe aus. In diesem Beispiel erstellen wir eine neue E-Mail-Benachrichtigung.

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Felder, die zum Hinzufügen einer Aktionsgruppe erforderlich sind](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot: Erforderliche Elemente zum Hinzufügen einer E-Mail- oder SMS-Nachrichtenwarnung](../media/alert-9.png)

6. Klicken Sie unten auf der Seite auf **OK**. Sie haben erfolgreich einen Aktionsgruppe erstellt. 

Nun, da Sie eine Aktionsgruppe konfiguriert haben, konfigurieren wir die Warnungsschwellenwerte für den Schlüsseltresor. 

### <a name="configure-alert-thresholds"></a>Konfigurieren von Warnungsschwellenwerten 

1. Wählen Sie Ihre Key Vault-Ressource im Azure-Portal aus, und klicken Sie unter **Überwachung** auf **Warnungen**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Abschnitt „Überwachung“ mit der Menüoption „Warnungen“](../media/alert-10.png)

2. Wählen Sie **Neue Warnungsregel** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Schaltfläche „+ Neue Warnungsregel“](../media/alert-11.png)

3. Legen Sie den Bereich für Ihre Warnungsregel fest. Sie können einen einzelnen Tresor oder mehrere Tresore auswählen. 

> [!IMPORTANT]
> Beachten Sie, dass sich bei Auswahl mehrerer Tresore als Bereich für Ihre Warnungen alle ausgewählten Tresore in derselben Region befinden müssen. Sie müssen für Tresore in unterschiedlichen Regionen separate Warnungsregeln konfigurieren. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Auswählen eines Tresors](../media/alert-12.png)

4. Wählen Sie die Bedingungen für Ihre Warnungen aus. Sie können beliebige der folgenden Signale auswählen und Ihre Warnungslogik definieren. Das Key Vault-Team empfiehlt eine Konfiguration der folgenden Warnungsschwellenwerte. 

    + Key Vault-Verfügbarkeit fällt unter 100 % (statischer Schwellenwert)
    + Key Vault-Wartezeit übersteigt 500 ms (statischer Schwellenwert) 
    + „Tresorauslastung insgesamt“ übersteigt 75 % (statischer Schwellenwert) 
    + „Tresorauslastung insgesamt“ überschreitet den Durchschnitt (dynamischer Schwellenwert)
    + „Fehlercodes gesamt“ liegt über dem Durchschnitt (dynamischer Schwellenwert) 

> [!div class="mx-imgBorder"]
> ![Screenshot: Auswählen von Bedingungen für Warnungen](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Beispiel 1: Konfigurieren eines statischen Warnungsschwellenwerts für die Latenzzeit

Wählen Sie als Signalname **Wartezeit für Dienst-API gesamt** aus.
> [!div class="mx-imgBorder"]
> ![Screenshot: Signalname „Wartezeit für Dienst-API gesamt“](../media/alert-14.png)

Beachten Sie die folgenden Konfigurationsparameter.

+ Legen Sie den Schwellenwert auf **Statisch** fest. 
+ Legen Sie den Operator auf **Größer als** fest.
+ Legen Sie den Aggregationstyp auf **Durchschnitt** fest.
+ Legen Sie den Wert des Schwellenwerts auf **500** fest.
+ Legen Sie den Aggregationszeitraum auf **5 Minuten** fest.
+ Legen Sie die Auswertungshäufigkeit auf **1 Minute** fest.
+ Wählen Sie **Fertig** aus.  

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene konfigurierte Warnungslogik](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Beispiel 2: Konfigurieren eines dynamischen Warnungsschwellenwerts für die Tresorauslastung 

Wenn Sie eine dynamische Warnung verwenden, können Sie historische Daten zum ausgewählten Schlüsseltresor anzeigen. Der blaue Bereich stellt die durchschnittliche Auslastung Ihres Schlüsseltresors dar. Der rote Bereich zeigt Spitzen, die zum Auslösen einer Warnung führen würden, wenn weitere Kriterien in der Warnungskonfiguration erfüllt sind. Die roten Punkte zeigen Verstöße, bei denen die Kriterien für die Warnung innerhalb des aggregierten Zeitfensters erfüllt waren. Sie können eine Warnung so konfigurieren, dass sie nach einer bestimmten Anzahl von Verstößen innerhalb eines festgelegten Zeitraums ausgelöst wird. Wenn Sie vorherige Daten nicht einbeziehen möchten, können Sie alte Daten über eine Option in den erweiterten Einstellungen ausschließen. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Graph der Gesamttresorauslastung](../media/alert-16.png)

Beachten Sie die folgenden Konfigurationsparameter.

+ Legen Sie den Schwellenwert auf **Dynamisch** fest. 
+ Legen Sie den Operator auf **Größer als** fest.
+ Legen Sie den Aggregationstyp auf **Durchschnitt** fest.
+ Legen Sie die Schwellenwertempfindlichkeit auf **Mittel** fest.
+ Legen Sie den Aggregationszeitraum auf **5 Minuten** fest.
+ Legen Sie die Auswertungshäufigkeit auf **1 Minute** fest.
+ **Optional**: Konfigurieren Sie erweiterte Einstellungen. 
+ Wählen Sie **Fertig** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot des Azure-Portals](../media/alert-17.png)

5. Hinzufügen der erstellten Aktionsgruppe

> [!div class="mx-imgBorder"]
> ![Screenshot: Hinzufügen einer Aktionsgruppe](../media/alert-18.png)

6. Aktivieren der Warnung und Zuweisen eines Schweregrads

> [!div class="mx-imgBorder"]
> ![Screenshot: Aktivieren der Warnung und Zuweisen eines Schweregrads](../media/alert-19.png)

7. Erstellen der Warnung 

### <a name="example-email-alert"></a>Beispiel-E-Mail-Warnung 

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Informationen, die zum Konfigurieren einer E-Mail-Warnung erforderlich sind](../media/alert-20.png)

## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Sie haben erfolgreich ein Überwachungsdashboard erstellt und Warnungen für Ihren Schlüsseltresor konfiguriert!

Nachdem Sie alle oben aufgeführten Schritte ausgeführt haben, sollten Sie jetzt E-Mail-Benachrichtigungen erhalten, wenn Ihr Schlüsseltresor die konfigurierten Warnungskriterien erfüllt. Ein entsprechendes Beispiel ist nachfolgend dargestellt. Verwenden Sie die in diesem Artikel eingerichteten Tools, um die Integrität Ihres Schlüsseltresors zu überwachen.

- [Überwachen von Key Vault](monitor-key-vault.md)
- [Überwachen von Key Vault-Datenverweisen](monitor-key-vault-reference.md)