---
title: Überwachen der Verfügbarkeit mit URL-Pingtests – Azure Monitor
description: Richten Sie Pingtests in Application Insights ein. Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert.
ms.topic: conceptual
ms.date: 07/13/2021
ms.reviewer: sdash
ms.openlocfilehash: 55e396f7d1af6bc05d722e90d7dad99b2ac8b2d7
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356547"
---
# <a name="monitor-availability-with-url-ping-tests"></a>Überwachen der Verfügbarkeit mit URL-Pingtests

Der Begriff *URL-Pingtest* ist irreführend. In diesen Tests wird die Verfügbarkeit Ihrer Website nicht mithilfe des Internet Control Message-Protokolls (ICMP) überprüft. Stattdessen verwenden sie erweiterte Funktionen von HTTP-Anforderungen, um zu überprüfen, ob ein Endpunkt reagiert. Sie messen die Leistung der jeweiligen Antwort. Sie können außerdem benutzerdefinierte Erfolgskriterien festlegen, die an erweiterte Features wie das Analysieren von abhängigen Anforderungen und das Zulassen von Wiederholungsversuchen gekoppelt sind.

Um einen Verfügbarkeitstest zu erstellen, müssen Sie eine vorhandene Application Insights-Ressource verwenden oder [eine Application Insights-Ressource erstellen](create-new-resource.md).

> [!NOTE]
> URL-Pingtests werden als klassische Tests kategorisiert. Sie finden sie unter **Add Classic Test** (Klassischen Test hinzufügen) im Bereich **Verfügbarkeit**. Andere erweiterte Funktionen finden Sie unter [Standardtests (Vorschau)](availability-standard-tests.md).
 
## <a name="create-a-test"></a>Erstellen eines Tests

So erstellen Sie die erste Verfügbarkeitsanforderung
1. Öffnen Sie in Ihrer Application Insights-Ressource den Bereich **Verfügbarkeit**, und wählen Sie   **Add Classic Test** (Klassischen Test hinzufügen) aus.

    :::image type="content" source="./media/monitor-web-app-availability/create-test.png" alt-text="Screenshot des Bereichs „Verfügbarkeit“ und der Schaltfläche zum Hinzufügen eines klassischen Tests" lightbox ="./media/monitor-web-app-availability/create-test.png":::
1. Benennen Sie den Test, und wählen Sie **URL-Pingtest** als **SKU** aus.
1. Geben Sie die URL ein, die Sie testen möchten.
1. Passen Sie die (in der folgenden Tabelle beschriebenen) Einstellungen an Ihre Anforderungen an, und wählen Sie **Erstellen** aus.

   |Einstellung| Erklärung |
   |----|----|
   |**URL** |  Die URL kann zu einer beliebigen Webseite führen, die Sie testen möchten, aber sie muss im öffentlichen Internet sichtbar sein. Die URL kann eine Abfragezeichenfolge enthalten. Sie können beispielsweise Ihre Datenbank abfragen. Wenn die URL in eine Umleitung aufgelöst wird, können Sie bis zu 10 Umleitungen nachverfolgen.|
   |**Abhängige Anforderungen analysieren**| Der Test fordert Bilder, Skripts, Formatdateien und andere Dateien an, die Teil der zu testenden Webseite sind. Die aufgezeichnete Antwortzeit enthält auch die Zeit, die zum Abrufen dieser Dateien erforderlich ist. Der Test führt zu einem Fehler, wenn eine dieser Ressourcen innerhalb des Zeitlimits für den gesamten Test nicht erfolgreich heruntergeladen werden kann. Wenn die Option nicht aktiviert ist, wird beim Test nur die Datei unter der von Ihnen angegebenen URL angefordert. Wenn diese Option aktiviert wird, wird strenger geprüft. Der Test führt möglicherweise in Fällen zu Fehlern, die durch manuelles Durchsuchen der Website nicht bemerkt werden.
   |**Enable retries** (Wiederholungen aktivieren)|Wenn der Test nicht erfolgreich ist, wird er nach kurzer Zeit wiederholt. Nur wenn drei aufeinander folgende Versuche scheitern, wird ein Fehler gemeldet. Nachfolgende Tests werden dann in der üblichen Häufigkeit ausgeführt. Die Wiederholung wird bis zum nächsten Erfolg vorübergehend eingestellt. Diese Regel wird an jedem Teststandort unabhängig angewendet. *Es wird empfohlen, diese Option zu verwenden.* Im Durchschnitt treten etwa 80 % der Fehler bei einer Wiederholung nicht mehr auf.|
   |**Testhäufigkeit**| Diese Einstellung legt fest, wie oft der Test von jedem Teststandort aus ausgeführt wird. Mit einer Standardfrequenz von fünf Minuten und fünf Teststandorten wird Ihre Website im Durchschnitt jede Minute getestet.|
   |**Teststandorte**| Die Werte für diese Einstellung sind die Orte, von denen Server Webanforderungen an Ihre URL senden. *Es wird empfohlen, mindestens fünf Teststandorte festzulegen*, um sicherzustellen, dass Sie Probleme mit Ihrer Website von Netzwerkproblemen unterscheiden können. Sie können bis zu 16 Standorte auswählen.

Wenn Ihre URL im öffentlichen Internet nicht sichtbar ist, können Sie entscheiden, Ihre Firewall zu öffnen, sodass nur die Testtransaktionen sie passieren können. Weitere Informationen zu Firewallausnahmen für Agents für Verfügbarkeitstests finden Sie im [Leitfaden für IP-Adresstests](./ip-addresses.md#availability-tests).

> [!NOTE]
> Es wird dringend empfohlen, Tests von mehreren Standorten auszuführen (mindestens 5 Standorte). Dies dient dazu, Fehlalarme zu vermeiden, die durch vorübergehende Probleme an einem bestimmten Standort entstehen können. Darüber hinaus haben wir festgestellt, dass in einer optimalen Konfiguration die *Anzahl von Teststandorten dem Warnungsschwellenwert für Standorte + 2 entspricht*.

## <a name="success-criteria"></a>Erfolgskriterien

|Einstellung| Erklärung |
|----|----|
| **Testtimeout** |Reduzieren Sie diesen Wert, um über langsame Antworten benachrichtigt zu werden. Der Test wird als ein Fehler gezählt, wenn die Antworten von Ihrer Website nicht innerhalb dieses Zeitraums empfangen wurden. Bei Auswahl von **Abhängige Anforderungen analysieren** müssen alle Bilder, Styledateien, Skripts und anderen abhängigen Ressourcen innerhalb dieses Zeitraums empfangen werden.|
| **HTTP-Antwort** | Der zurückgegebene Statuscode, der als Erfolg gezählt wird. 200 ist der Code, der angibt, dass eine normale Webseite zurückgegeben wurde.|
| **Inhaltsübereinstimmung** | Mit diesem Test vergewissern Sie sich, dass in jeder Antwort eine exakte Übereinstimmung unter Berücksichtigung der Groß- und Kleinschreibung vorkommt. Dies muss eine Zeichenfolge im Klartext und ohne Platzhalter sein (z. B. „Willkommen!“). Vergessen Sie nicht, diese zu aktualisieren, wenn sich der Seiteninhalt ändert. *Bei Inhaltsübereinstimmungen werden nur englische Zeichen unterstützt.* |

## <a name="alerts"></a>Alerts

|Einstellung| Erklärung |
|----|----|
|**Near-realtime (Preview)** (Nahezu in Echtzeit (Vorschauversion)) | Es wird empfohlen, Warnungen zu verwenden, die nahezu in Echtzeit funktionieren. Sie konfigurieren diesen Warnungstyp, nachdem Sie den Verfügbarkeitstest erstellt haben.  |
|**Schwellenwert für den Warnungsspeicherort**| Das optimale Verhältnis zwischen dem Schwellenwert für den Warnungsspeicherort und der Anzahl von Teststandorten lautet *Warnungsschwellenwert für Standort = Anzahl von Teststandorten - 2*, bei einer Mindestanzahl von fünf Teststandorten.|

## <a name="location-population-tags"></a>Auffüllungstags für den Standort

Beim Bereitstellen eines URL-Pingtests für die Verfügbarkeit mithilfe von Azure Resource Manager können für das Attribut für den geografischen Standort die folgenden Auffüllungstags verwendet werden.

### <a name="azure-government"></a>Azure Government

| `Display name`   | Auffüllungsname     |
|----------------|---------------------|
| US Government, Virginia | usgov-va-azr        |
| US Gov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| USDoD, Osten     | usgov-ddeast-azr    |
| USDoD, Mitte  | usgov-ddcentral-azr |

### <a name="azure-china"></a>Azure China

| `Display name`   | Auffüllungsname     |
|----------------|---------------------|
| China, Osten     | mc-cne-azr          |
| China, Osten 2   | mc-cne2-azr         |
| China, Norden    | mc-cnn-azr          |
| China, Norden 2  | mc-cnn2-azr         |

### <a name="azure"></a>Azure

| `Display name`                           | Auffüllungsname   |
|----------------------------------------|-------------------|
| Australien, Osten                         | emea-au-syd-edge  |
| Brasilien Süd                           | latam-br-gru-edge |
| USA (Mitte)                             | us-fl-mia-edge    |
| Asien, Osten                              | apac-hk-hkn-azr   |
| East US                                | us-va-ash-azr     |
| Frankreich, Süden (ehemals Frankreich, Mitte) | emea-ch-zrh-edge  |
| Frankreich, Mitte                         | emea-fr-pra-edge  |
| Japan, Osten                             | apac-jp-kaw-edge  |
| Nordeuropa                           | emea-gb-db3-azr   |
| USA Nord Mitte                       | us-il-ch1-azr     |
| USA Süd Mitte                       | us-tx-sn1-azr     |
| Asien, Südosten                         | apac-sg-sin-azr   |
| UK, Westen                                | emea-se-sto-edge  |
| Europa, Westen                            | emea-nl-ams-azr   |
| USA (Westen)                                | us-ca-sjc-azr     |
| UK, Süden                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Anzeigen der Verfügbarkeitstestergebnisse

Sie können die Ergebnisse von Verfügbarkeitstests sowohl mit Linien- als auch mit Punktdiagrammansichten visualisieren.

Klicken Sie nach einigen Minuten auf **Aktualisieren**, um Ihre Testergebnisse anzuzeigen.

:::image type="content" source="./media/monitor-web-app-availability/refresh.png" alt-text="Screenshot der Seite „Verfügbarkeit“ mit hervorgehobener Schaltfläche „Aktualisieren“" lightbox="./media/monitor-web-app-availability/refresh.png":::

Das Punktdiagramm zeigt Stichproben der Testergebnisse an, die Diagnosedetails zu Testschritten enthalten. Die Test-Engine speichert Diagnosedetails für Tests mit Fehlern. Für erfolgreiche Tests werden Diagnosedetails für eine Teilmenge der Ausführungen gespeichert. Zeigen Sie mit dem Mauszeiger auf einen der grünen oder roten Punkte, um den Testnamen und den Standort anzuzeigen.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Screenshot der Linienansicht" border="false":::

Wählen Sie einen bestimmten Test oder Standort aus, oder verringern Sie den Zeitraum, um weitere Ergebnisse für den gewünschten Zeitraum anzuzeigen. Verwenden Sie den Such-Explorer, um Ergebnisse von allen Ausführungen anzuzeigen, oder Analytics-Abfragen, um benutzerdefinierte Berichte für diese Daten auszuführen.

## <a name="inspect-and-edit-tests"></a>Überprüfen und Bearbeiten von Tests

Wenn Sie einen Test bearbeiten, vorübergehend deaktivieren oder löschen möchten, wählen Sie die drei Punkte ( **...** ) neben dem jeweiligen Testnamen aus. Es kann bis zu 20 Minuten dauern, bis Konfigurationsänderungen an alle Test-Agents weitergegeben werden.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Screenshot von Testdetails mit Optionen zum Bearbeiten und Deaktivieren eines Tests" border="false":::

Eventuell möchten Sie Verfügbarkeitstests oder die damit verknüpften Warnungsregeln deaktivieren, während Sie Ihren Dienst warten.

## <a name="actions-if-you-see-failures"></a>Aktionen bei Fehlern

Einen roten Punkt auswählen.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Screenshot der Registerkarte mit Details zu End-to-End-Transaktionen" border="false":::

Aus einem Verfügbarkeitstestergebnis können Sie die Transaktionsdetails für alle Komponenten ablesen. Anschließend können Sie folgende Aktionen ausführen:

* Überprüfen Sie den Bericht zur Problembehandlung, um zu ermitteln, was möglicherweise dazu geführt hat, dass der Test fehlerhaft war, obwohl Ihre Anwendung weiterhin verfügbar ist.
* Untersuchen Sie die vom Server erhaltene Antwort.
* Diagnostizieren Sie Fehler mit korrelierten serverseitigen Telemetriedaten, die während der Verarbeitung des fehlerhaften Verfügbarkeitstests gesammelt wurden.
* Protokollieren Sie in Git oder Azure Boards ein Problem oder ein Arbeitselement, um das Problem nachzuverfolgen. Der Fehler enthält einen Link zu diesem Ereignis.
* Öffnen Sie das Webtestergebnis in Visual Studio.

Weitere Informationen zur Diagnose von End-to-End-Transaktionen finden Sie in der [Dokumentation zur Transaktionsdiagnose](./transaction-diagnostics.md).

Wählen Sie die Ausnahmezeile aus, um Details zur serverseitigen Ausnahme anzuzeigen, die beim synthetischen Verfügbarkeitstest zu dem Fehler geführt hat. Sie können auch die [Debugmomentaufnahme](./snapshot-debugger.md) abrufen, um eine umfangreichere Diagnose auf Codeebene durchzuführen.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Screenshot der serverseitigen Diagnose":::

Zusätzlich zu den unformatierten Ergebnissen können Sie im [Metrik-Explorer](../essentials/metrics-getting-started.md) zwei wichtige Verfügbarkeitsmetriken abrufen:

- **Verfügbarkeit:** Prozentsatz der erfolgreichen Tests für alle Testausführungen
- **Testdauer:** Durchschnittliche Testdauer für alle Ausführungen

## <a name="automation"></a>Automation

* [Verwenden Sie PowerShell-Skripts zum automatischen Einrichten eines Verfügbarkeitstests](./powershell.md#add-an-availability-test).
* Richten Sie einen [Webhook](../alerts/alerts-webhooks.md) ein, der bei einer Warnung aufgerufen wird.


## <a name="next-steps"></a>Nächste Schritte

* [Verfügbarkeitswarnungen](availability-alerts.md)
* [Multi-step web tests (Mehrstufige Webtests)](availability-multistep.md)
* [Problembehandlung](troubleshoot-availability.md)
* [Webtests: Azure Resource Manager-Vorlage](/azure/templates/microsoft.insights/webtests?tabs=json)
