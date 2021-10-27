---
title: Konfigurieren von Azure Key Vault-Warnungen
description: Erfahren Sie, wie Sie Warnungen erstellen, um die Integrität Ihres Schlüsseltresors zu überwachen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: c7661f91475b85ad8d606770ff74777bb3b8abab
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994269"
---
# <a name="configure-azure-key-vault-alerts"></a>Konfigurieren von Azure Key Vault-Warnungen

Sobald Sie damit begonnen haben, Ihre Produktionsgeheimnisse in Azure Key Vault zu speichern, sollten Sie durch eine Überwachung der Integrität Ihres Schlüsseltresors sicherstellen, dass Ihr Dienst wie vorgesehen funktioniert. 

Wenn Sie damit beginnen, Ihren Dienst zu skalieren, steigt die Anzahl von Anforderungen, die an Ihren Schlüsseltresor gesendet werden. Dieser Anstieg kann die Wartezeit Ihrer Anforderungen erhöhen. In Extremfällen kann dies dazu führen, dass Ihre Anforderungen gedrosselt werden und die Leistung Ihres Diensts beeinträchtigt ist. Sie müssen es außerdem erfahren, wenn Ihr Schlüsseltresor eine ungewöhnliche Anzahl von Fehlercodes sendet, damit Sie bei Problemen mit einer Zugriffsrichtlinie oder Firewallkonfiguration schnell handeln können. 

In diesem Artikel wird gezeigt, wie Sie Warnungen bei bestimmten Schwellenwerten konfigurieren, damit Ihr Team sofort benachrichtigt wird, wenn sich Ihr Schlüsseltresor in einem fehlerhaften Zustand befindet. Sie können Warnungen konfigurieren, mit denen eine E-Mail gesendet (vorzugsweise an eine Verteilerliste für ein Team), eine Azure Event Grid-Benachrichtigung ausgelöst, eine Telefonnummer angerufen oder eine SMS gesendet wird. 

Sie können zwischen den folgenden Warnungstypen wählen:

- Eine statische Warnung, die auf einem festen Wert basiert
- Eine dynamische Warnung, die Sie benachrichtigt, wenn für eine überwachte Metrik der Durchschnittsgrenzwert für Ihren Schlüsseltresor innerhalb eines definierten Zeitraums mit einer festgelegten Häufigkeit überschritten wird

> [!IMPORTANT]
> Es kann bis zu 10 Minuten dauern, bis neu konfigurierte Warnungen damit beginnen, Benachrichtigungen zu versenden. 

In diesem Artikel geht es um Warnungen für Key Vault. Informationen über Key Vault-Erkenntnisse, die sowohl Protokolle als auch Metriken in einer globalen Überwachungslösung kombinieren, finden Sie unter [Überwachen Ihres Schlüsseltresordiensts mit Key Vault Insights](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights).

## <a name="configure-an-action-group"></a>Konfigurieren einer Aktionsgruppe 

Eine Aktionsgruppe ist eine konfigurierbare Liste mit Benachrichtigungen und Eigenschaften. Der erste Schritt beim Konfigurieren von Warnungen ist das Erstellen einer Aktionsgruppe und die Auswahl eines Warnungstyps:

1. Melden Sie sich beim Azure-Portal an.
2. Suchen Sie im Suchfeld nach **Warnungen**.
3. Wählen Sie **Aktionen verwalten** aus.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Hervorgehobene Schaltfläche „Aktionen verwalten“](../media/alert-6.png)

4. Klicken Sie auf **+ Aktionsgruppe hinzufügen**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Hervorgehobene Schaltfläche zum Hinzufügen einer Aktionsgruppe](../media/alert-7.png)

5. Wählen Sie den **Aktionstyp**-Wert für Ihre Aktionsgruppe aus. In diesem Beispiel erstellen wir eine E-Mail- und SMS-Warnung. Wählen Sie **E-Mail/SMS/Push/Sprachanruf** aus.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Hervorgehobene Auswahl zum Hinzufügen einer Aktionsgruppe](../media/alert-8.png)

6. Geben Sie im Dialogfeld E-Mail- und SMS-Details ein, und wählen Sie dann **OK** aus.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Auswahl zum Hinzufügen einer E-Mail- und SMS-Warnungsmeldung](../media/alert-9.png)

## <a name="configure-alert-thresholds"></a>Konfigurieren von Warnungsschwellenwerten 

Als Nächstes erstellen Sie eine Regel und konfigurieren die Schwellenwerte, die eine Warnung auslösen sollen:

1. Wählen Sie im Azure-Portal Ihre Key Vault-Ressource aus und wählen Sie dann unter **Überwachung** die Option **Warnungen**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Abschnitt „Überwachung“ mit der Menüoption „Warnungen“](../media/alert-10.png)

2. Wählen Sie **Neue Warnungsregel** aus.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Schaltfläche zum Hinzufügen einer neuen Warnungsregel](../media/alert-11.png)

3. Legen Sie den Bereich für Ihre Warnungsregel fest. Sie können einen oder mehrere Tresore auswählen. 

   > [!IMPORTANT]
   > Bei der Auswahl mehrerer Tresore als Bereich für Ihre Warnungen müssen sich alle ausgewählten Tresore in derselben Region befinden. Für Tresore in unterschiedlichen Regionen müssen Sie separate Warnungsregeln konfigurieren. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Auswählen eines Tresors](../media/alert-12.png)

4. Wählen Sie die Schwellenwerte aus, die die Logik für Ihre Warnungen definieren, und wählen Sie dann **Hinzufügen**. Das Key Vault-Team empfiehlt die Konfiguration der folgenden Warnungsschwellenwerte: 

    + Key Vault-Verfügbarkeit fällt unter 100 Prozent (statischer Schwellenwert)
    + Key Vault-Wartezeit übersteigt 1000 ms (statischer Schwellenwert) 
    + „Tresorauslastung insgesamt“ übersteigt 75 Prozent (statischer Schwellenwert) 
    + „Tresorauslastung insgesamt“ überschreitet den Durchschnitt (dynamischer Schwellwert)
    + „Fehlercodes gesamt“ liegt über dem Durchschnitt (dynamischer Schwellwert) 

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Auswählen von Bedingungen für Warnungen](../media/alert-13.png)

### <a name="example-configure-a-static-alert-threshold-for-latency"></a>Beispiel: Konfigurieren eines statischen Warnungsschwellenwerts für die Wartezeit

1. Wählen Sie als Signalname **Wartezeit für Dienst-API gesamt** aus.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Auswählen eines Signalnamens](../media/alert-14.png)

1. Verwenden Sie die folgenden Konfigurationsparameter:

   + Legen Sie **Schwellenwert** auf **Statisch** fest. 
   + Legen Sie **Operator** auf **Größer als** fest.
   + Legen Sie **Aggregationstyp** auf **Durchschnitt** fest.
   + Legen Sie **Schwellenwert** auf **1000** fest.
   + Legen Sie **Aggregationsgranularität (Zeitraum)** auf **5 Minuten** fest.
   + Legen Sie **Häufigkeit der Auswertung** auf **Jede Minute** fest.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: konfigurierte Logik für einen statischen Warnungsschwellenwert](../media/alert-15.png)

1. Wählen Sie **Fertig** aus.  

### <a name="example-configure-a-dynamic-alert-threshold-for-vault-saturation"></a>Beispiel: Konfigurieren eines dynamischen Warnungsschwellenwerts für die Tresorauslastung 

Wenn Sie eine dynamische Warnung verwenden, können Sie historische Daten zum ausgewählten Schlüsseltresor anzeigen. Der blaue Bereich stellt die durchschnittliche Auslastung Ihres Schlüsseltresors dar. Der rote Bereich zeigt Spitzen, die zum Auslösen einer Warnung führen würden, wenn weitere Kriterien in der Warnungskonfiguration erfüllt sind. Die roten Punkte zeigen Verstöße, bei denen die Kriterien für die Warnung innerhalb des aggregierten Zeitfensters erfüllt waren. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Graph der Gesamttresorauslastung](../media/alert-16.png)

Sie können eine Warnung so konfigurieren, dass sie nach einer bestimmten Anzahl von Verstößen innerhalb eines festgelegten Zeitraums ausgelöst wird. Wenn Sie vorherige Daten nicht einbeziehen möchten, können Sie sie über eine Option in den erweiterten Einstellungen ausschließen. 

1. Verwenden Sie die folgenden Konfigurationsparameter:

   + Legen Sie **Schwellenwert** auf **Dynamisch** fest. 
   + Legen Sie **Operator** auf **Größer als** fest.
   + Legen Sie **Aggregationstyp** auf **Durchschnitt** fest.
   + Legen Sie **Schwellenwertempfindlichkeit** auf **Mittel** fest.
   + Legen Sie **Aggregationsgranularität (Zeitraum)** auf **5 Minuten** fest.
   + Legen Sie **Häufigkeit der Auswertung** auf **Alle 5 Minuten** fest.
   + Konfigurieren Sie die **erweiterten Einstellungen** (optional). 

   > [!div class="mx-imgBorder"]
   > ![Screenshot: konfigurierte Logik für einen dynamischen Warnungsschwellenwert](../media/alert-17.png)

1. Wählen Sie **Fertig** aus.

1. Wählen Sie **Hinzufügen** aus, um die von Ihnen konfigurierte Aktionsgruppe hinzuzufügen.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Schaltfläche zum Hinzufügen einer Aktionsgruppe](../media/alert-18.png)

1. Aktivieren Sie in den Warnungsdetails die Warnung, und weisen Sie einen Schweregrad zu.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Aktivieren der Warnung und Zuweisen eines Schweregrads](../media/alert-19.png)

1. Erstellen Sie die Warnung. 

### <a name="example-email-alert"></a>Beispiel-E-Mail-Warnung 

Wenn Sie alle oben genannten Schritte ausgeführt haben, erhalten Sie E-Mail-Benachrichtigungen, wenn Ihr Schlüsseltresor die konfigurierten Warnungskriterien erfüllt. Die folgende E-Mail-Warnung ist ein Beispiel. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Informationen, die zum Konfigurieren einer E-Mail-Warnung erforderlich sind](../media/alert-20.png)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die in diesem Artikel eingerichteten Tools, um die Integrität Ihres Schlüsseltresors zu überwachen:

- [Überwachen von Key Vault](monitor-key-vault.md)
- [Überwachen von Key Vault-Datenverweisen](monitor-key-vault-reference.md)
