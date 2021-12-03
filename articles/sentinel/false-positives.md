---
title: Behandeln falsch positiver Ergebnisse in Microsoft Sentinel
description: Erfahren Sie, wie Sie falsch positive Ergebnisse in Microsoft Sentinel beheben, indem Sie Automatisierungsregeln erstellen oder Analyseregeln ändern, um Ausnahmen anzugeben.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 009cb9d66f4294bc72bafdcf515a68131d9b7e38
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521157"
---
# <a name="handle-false-positives-in-microsoft-sentinel"></a>Behandeln falsch positiver Ergebnisse in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Microsoft Sentinel-Analyseregeln](detect-threats-built-in.md) benachrichtigen Sie, wenn in Ihrem Netzwerk etwas Verdächtiges auftritt. Keine Analyseregel ist perfekt, und Sie werden einige falsch positive Ergebnisse, die sich nicht vermeiden lassen, behandeln müssen. In diesem Artikel wird beschrieben, wie Sie falsch positive Ergebnisse entweder mithilfe der Automatisierung oder durch Ändern geplanter Analyseregeln behandeln können.

## <a name="false-positive-causes-and-prevention"></a>Ursachen und Prävention falsch positiver Ergebnisse

Selbst bei einer ordnungsgemäß erstellten Analyseregel stammen falsch positive Ergebnisse häufig von bestimmten Entitäten wie Benutzern oder IP-Adressen, die von der Regel ausgeschlossen werden sollten.

Zu den häufigen Szenarios gehören:

- Normale Aktivitäten bestimmter Benutzer (in der Regel Dienstprinzipale) zeigen ein Muster, das verdächtig erscheint.
- Beabsichtigte Sicherheitsüberprüfungsaktivitäten, die von bekannten IP-Adressen stammen, werden als schädlich erkannt.
- Bei einer Regel, mit der private IP-Adressen ausgeschlossen werden, sollten auch einige interne IP-Adressen ausgeschlossen werden, die nicht privat sind.

In diesem Artikel werden zwei Methoden zum Vermeiden falsch positiver Ergebnisse beschrieben:

- Mit **Automatisierungsregeln** werden Ausnahmen erstellt, ohne die Analyseregeln zu ändern.
- **Änderungen an geplanten Analyseregeln** ermöglichen detailliertere und dauerhafte Ausnahmen.

In der folgenden Tabelle sind die Merkmale der einzelnen Methoden beschrieben:


|Methode|Merkmal|
|-|-|
|**Automatisierungsregeln**|<ul><li>Können auf mehrere Analyseregeln angewendet werden.</li><li>Bieten ein Überwachungsprotokoll. Ausnahmen verhindern die Erstellung von Incidents. Warnungen werden jedoch weiterhin zu Überwachungszwecken aufgezeichnet.</li><li>Werden häufig von Analysten generiert.</li><li>Ermöglichen das Anwenden von Ausnahmen für einen begrenzten Zeitraum. Wartungsarbeiten können beispielsweise falsch positive Ergebnisse auslösen, die außerhalb des Wartungszeitraums echte Incidents wären.</li></ul>|
|**Änderungen an Analyseregeln**|<ul><li>Ermöglichen erweiterte boolesche Ausdrücke und subnetzbasierte Ausnahmen.</li><li>Ermöglichen die Verwendung von Watchlists für eine zentrale Verwaltung von Ausnahmen.</li><li>In der Regel ist die Implementierung durch SOC-Techniker (Security Operations Center) erforderlich.</li><li>Diese Lösung ist die flexibelste und vollständigste Lösung für falsch positive Ergebnisse, ist aber auch komplexer.</li></ul>|

## <a name="add-exceptions-by-using-automation-rules"></a>Hinzufügen von Ausnahmen mithilfe von Automatisierungsregeln

Die einfachste Möglichkeit, eine Ausnahme hinzuzufügen, besteht im [Hinzufügen einer Automatisierungsregel](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules), wenn ein falsch positiver Incident angezeigt wird.

So fügen Sie eine Automatisierungsregel hinzu, um ein falsch positives Ergebnis zu behandeln

1. Wählen Sie in Microsoft Sentinel unter **Incidents** den Incident aus, für den Sie eine Ausnahme erstellen möchten.
1. Wählen Sie **Automatisierungsregel erstellen** aus.
1. Ändern Sie optional auf der Randleiste **Neue Automatisierungsregel erstellen** den Namen der neuen Regel, der die Ausnahme definieren soll, und verwenden Sie nicht einfach den Namen der Warnungsregel.
1. Fügen Sie unter **Bedingungen** optional weitere **Namen von Analyseregeln** hinzu, auf die die Ausnahme angewendet werden soll.
1. Auf der Randleiste werden jeweils die Entitäten im aktuellen Incident angezeigt, die möglicherweise das falsch positive Ergebnis verursacht haben. Übernehmen Sie die automatischen Vorschläge, oder ändern Sie diese, um die Ausnahme zu optimieren. Beispielsweise können Sie eine Bedingung für eine IP-Adresse so ändern, dass sie für ein ganzes Subnetz gilt.

   :::image type="content" source="media/false-positives/create-rule.png" alt-text="Screenshot: Erstellen einer Automatisierungsregel für einen Incident in Microsoft Sentinel":::

1. Nachdem Sie den Trigger definiert haben, können Sie den Vorgang fortsetzen und die Funktion der Regel definieren:

   :::image type="content" source="media/false-positives/apply-rule.png" alt-text="Screenshot: Beenden des Erstellens und Anwendens einer Automatisierungsregel in Microsoft Sentinel":::

   - Die Regel ist bereits so konfiguriert, dass sie den Incident schließt, der die Ausnahmekriterien erfüllt.
   - Sie können dem automatisch geschlossenen Incident einen Kommentar hinzufügen, in dem die Ausnahme erläutert wird. Sie können z. B. angeben, dass der Incident von einer bekannten Verwaltungsaktivität ausgelöst wurde.
   - Standardmäßig ist festgelegt, dass die Regel nach 24 Stunden automatisch abläuft. Dieser Ablauftermin entspricht möglicherweise Ihren Vorstellungen und verringert gleichzeitig die Wahrscheinlichkeit falsch negativer Fehler. Wenn die Ausnahme jedoch länger bestehen soll, legen Sie den **Regelablauf** auf einen späteren Zeitpunkt fest.

1. Wählen Sie **Anwenden** aus, um die Ausnahme zu aktivieren.

> [!TIP]
> Sie können auch eine Automatisierungsregel von Grund auf neu erstellen, ohne mit einem Incident zu beginnen. Wählen Sie im linken Navigationsmenü von Microsoft Sentinel die Option **Automatisierung** aus, und wählen Sie dann **Erstellen** > **Neue Regel hinzufügen** aus.

## <a name="add-exceptions-by-modifying-analytics-rules"></a>Hinzufügen von Ausnahmen durch Ändern von Analyseregeln

Eine weitere Möglichkeit zum Implementieren von Ausnahmen ist das Ändern der Abfrage von Analyseregeln. Sie können Ausnahmen direkt in die Regel einschließen oder am besten (sofern machbar) einen Verweis auf eine [Watchlist](watchlists.md#use-watchlists-in-analytics-rules) verwenden. Anschließend können Sie die Ausnahmeliste in der Watchlist verwalten.

### <a name="modify-the-query"></a>Ändern der Abfrage

Um vorhandene Analyseregeln zu bearbeiten, wählen Sie im linken Navigationsmenü von Microsoft Sentinel die Option **Automatisierung** aus. Wählen Sie die Regel aus, die Sie bearbeiten möchten, und wählen Sie dann unten rechts **Bearbeiten** aus, um den **Assistenten für Analyseregeln** zu öffnen.

Ausführliche Anweisungen zum Verwenden des **Assistenten für Analyseregeln** zum Erstellen und Bearbeiten von Analyseregeln finden Sie unter [Erstellen von benutzerdefinierten Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md).

Um in einer typischen Regelpräambel eine Ausnahme zu implementieren, können Sie eine Bedingung wie `where IPAddress !in ('<ip addresses>')` ziemlich am Anfang der Regelabfrage hinzufügen. Diese Zeile schließt bestimmte IP-Adressen aus der Regel aus.

```kusto
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in ('10.0.0.8', '192.168.12.1')
...
```

Diese Art von Ausnahme ist nicht auf IP-Adressen beschränkt. Sie können mithilfe des Felds `UserPrincipalName` bestimmte Benutzer oder mithilfe von `AppDisplayName` bestimmte Apps ausschließen.

Sie können auch mehrere Attribute ausschließen. Verwenden Sie beispielsweise Folgendes, um Warnungen von der IP-Adresse `10.0.0.8` oder dem Benutzer `user@microsoft.com` auszuschließen:

```kusto
| where IPAddress !in ('10.0.0.8')
| where UserPrincipalName != 'user@microsoft.com'
```

Um ggf. eine differenziertere Ausnahme zu implementieren und die Wahrscheinlichkeit falsch negativer Ergebnisse zu verringern, können Sie Attribute kombinieren. Die folgende Ausnahme gilt nur, wenn beide Werte in derselben Warnung angezeigt werden:

```kusto
| where IPAddress != '10.0.0.8' and UserPrincipalName != 'user@microsoft.com'
```

### <a name="exclude-subnets"></a>Ausschließen von Subnetzen

Das Ausschließen von IP-Adressbereichen, die von einer Organisation verwendet werden, erfordert den Ausschluss eines Subnetzes. Im folgenden Beispiel wird gezeigt, wie Sie Subnetze ausschließen.

Der Operator `ipv4_lookup` ist ein Anreicherungsoperator und kein Filteroperator. Die Zeile `where isempty(network)` führt die eigentliche Filterung aus, indem die Ereignisse untersucht werden, die keine Übereinstimmung zeigen.

```kusto
let subnets = datatable(network:string) [ "111.68.128.0/17", "5.8.0.0/19", ...];
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| evaluate ipv4_lookup(subnets, IPAddress, network, return_unmatched = true)
| where isempty(network)
...
```

### <a name="use-watchlists-to-manage-exceptions"></a>Verwenden von Watchlists zum Verwalten von Ausnahmen

Sie können eine Watchlist verwenden, um die Liste der Ausnahmen außerhalb der eigentlichen Regel zu verwalten. Diese Lösung (sofern anwendbar) bietet folgende Vorteile:

- Ein Analyst kann Ausnahmen hinzufügen, ohne die Regel zu bearbeiten. Dies entspricht viel eher den bewährten SOC-Methoden.
- Dieselbe Watchlist kann für mehrere Regeln gelten, was eine zentrale Ausnahmeverwaltung ermöglicht.

Die Verwendung einer Watchlist ist mit der Verwendung einer direkten Ausnahme vergleichbar. Verwenden Sie `_GetWatchlist('<watchlist name>')` zum Aufrufen der Watchlist:

```kusto
let timeFrame = 1d;
let logonDiff = 10m;
let allowlist = (_GetWatchlist('ipallowlist') | project IPAddress);
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in (allowlist)
...
```

Mithilfe der Watchlist können Sie auch eine Subnetzfilterung ausführen. Im obigen Subnetzausschlusscode könnten Sie beispielsweise die Subnetzdefinition `datatable` durch eine Watchlist ersetzen:

```kusto
let subnets = _GetWatchlist('subnetallowlist');
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:
- [Verwenden von UEBA-Daten zum Analysieren falsch positiver Ergebnisse](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [Automatisierung der Vorfallbehandlung in Microsoft Sentinel mit Automatisierungsregeln](automate-incident-handling-with-automation-rules.md)
- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md)
- [Verwenden von Microsoft Sentinel-Watchlists](watchlists.md)
