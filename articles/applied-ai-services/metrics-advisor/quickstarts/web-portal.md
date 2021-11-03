---
title: 'Schnellstart: Metrics Advisor-Webportal'
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zu den ersten Schritten mit dem Metrics Advisor-Webportal.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom: mode-portal
ms.openlocfilehash: ca4edde32a8a1eb490eb8c62d23c22061ea5c706
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026617"
---
# <a name="quickstart-monitor-your-first-metric-by-using-the-web-portal"></a>Schnellstart: Überwachen Ihrer ersten Metrik mithilfe des Webportals

Wenn Sie eine Azure Metrics Advisor-Instanz bereitstellen, können Sie den Dienst über die APIs sowie über den webbasierten Arbeitsbereich verwenden. Der webbasierte Arbeitsbereich ermöglicht einen einfachen und schnellen Einstieg in die Verwendung des Diensts. Darüber hinaus können Sie hier visuell Einstellungen konfigurieren, Ihr Modell anpassen und Ursachenanalysen durchführen. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/cognitive-services)
* Wenn Sie über Ihre Azure-Abonnements verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource </a> im Azure-Portal, um Ihre Instanz von Metrics Advisor bereitzustellen.  

    
> [!TIP]
> * Die Bereitstellung Ihrer Metrics Advisor-Ressource kann zwischen zehn und 30 Minuten dauern. Wählen Sie nach der erfolgreichen Bereitstellung die Option **Zu Ressource wechseln** aus.
> * Wenn Sie über die REST-API mit dem Dienst interagieren möchten, benötigen Sie den Schlüssel und den Endpunkt der erstellten Ressource. Diese finden Sie in der erstellten Ressource auf der Registerkarte **Schlüssel und Endpunkte**.


In diesem Dokument wird eine SQL-Datenbank-Instanz als Beispiel für die Erstellung Ihres ersten Monitors verwendet.

## <a name="sign-in-to-your-workspace"></a>Anmelden in Ihrem Arbeitsbereich

Melden Sie sich nach der Erstellung Ihrer Ressource mit Ihrem Active Directory-Konto beim [Metrics Advisor-Portal](https://go.microsoft.com/fwlink/?linkid=2143774) an. Wählen Sie auf der Zielseite Ihr **Verzeichnis**, Ihr **Abonnement** und den soeben erstellten **Arbeitsbereich** und dann die Option **Erste Schritte** aus. Um Zeitreihendaten zu verwenden, wählen Sie im Menü auf der linken Seite **Datenfeed hinzufügen** aus.

 
Derzeit können Sie in jeder verfügbaren Region eine Metrics Advisor-Ressource erstellen. Sie können die Arbeitsbereiche im Metrics Advisor-Portal jederzeit wechseln.


## <a name="time-series-data"></a>Zeitreihendaten

Metrics Advisor bietet Connectors für verschiedene Datenquellen wie Azure SQL-Datenbank, Azure Data Explorer und Azure Table Storage. Die Schritte für die Verbindung von Daten sind für unterschiedliche Connectors zwar ähnlich, einige Konfigurationsparameter können jedoch variieren. Weitere Informationen finden Sie unter [Verbinden verschiedener Datenquellen](../data-feeds-from-different-sources.md).

In dieser Schnellstartanleitung wird als Beispiel eine SQL-Datenbank verwendet. Mit denselben Schritten können Sie auch Ihre eigenen Daten erfassen.


### <a name="data-schema-requirements-and-configuration"></a>Datenschemaanforderungen und -konfiguration

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>Konfigurieren der Verbindungseinstellungen und der Abfrage

[Fügen Sie die Datenfeeds hinzu](../how-tos/onboard-your-data.md), indem Sie eine Verbindung mit Ihrer Zeitreihendatenquelle herstellen. Wählen Sie die folgenden Parameter aus:

* **Quelltyp**: Die Art der Datenquelle, in der Ihre Zeitreihendaten gespeichert sind.
* **Granularität**: Das Intervall zwischen aufeinanderfolgenden Datenpunkten in Ihren Zeitreihendaten (beispielsweise jährlich, monatlich oder täglich). Das kürzeste unterstützte Intervall beträgt 60 Sekunden.
* **Ingest data since (UTC)** (Daten erfassen seit (UTC)): Die Startzeit für den ersten zu erfassenden Zeitstempel. 


:::image type="content" source="../media/connection-settings.png" alt-text="Screenshot der Verbindungseinstellungen." lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>Laden der Daten

Nachdem Sie die Verbindungs- und Abfragezeichenfolgen eingegeben haben, wählen Sie **Daten laden** aus. Metrics Advisor überprüft die Verbindung und Berechtigung zum Laden von Daten, überprüft die in der Abfrage verwendeten erforderlichen Parameter und überprüft den Spaltennamen aus der Datenquelle. 

Gehen Sie wie folgt vor, falls bei diesem Schritt ein Fehler auftritt:
1. Überprüfen Sie, ob die Verbindungszeichenfolge gültig ist. 
1. Vergewissern Sie sich, dass ausreichende Berechtigungen vorhanden sind und dass für die IP-Adresse des Erfassungsworkers der Zugriff gewährt wurde.
1. Überprüfen Sie, ob die erforderlichen Parameter (`@IntervalStart` und `@IntervalEnd`) in Ihrer Abfrage verwendet werden. 

### <a name="schema-configuration"></a>Schemakonfiguration

Wählen Sie nach dem Laden der Daten durch die Ausführung der Abfrage die entsprechenden Felder aus.


|Auswahl  |BESCHREIBUNG  |Hinweise  |
|---------|---------|---------|
|**Timestamp**     | Der Zeitstempel eines Datenpunkts. Wenn kein Zeitstempel angegeben ist, wird von Metrics Advisor stattdessen der Zeitstempel der Datenpunkterfassung verwendet. Sie können für jeden Datenfeed maximal eine Spalte als Zeitstempel angeben.        | Dies ist optional. Geben Sie maximal eine Spalte an.       |
|**Measure**     |  Die numerischen Werte im Datenfeed. Sie können für jeden Datenfeed mehrere Measures angeben, aber mindestens eine Spalte sollte als Measure ausgewählt werden.        | Geben Sie mindestens eine Spalte an.        |
|**Dimension**     | Enthält Kategoriewerte. Mit einer Kombination unterschiedlicher Wert wird eine bestimmte Zeitreihe mit nur einer Dimension identifiziert. Beispiele sind unter anderem Land, Sprache und Mandant. Sie können beliebig viele Spalten (oder auch keine Spalten) als Dimensionen auswählen. Wenn Sie als Dimension eine Spalte auswählen, die keine Zeichenfolge enthält, müssen Sie darauf achten, dass es zu keiner Dimensionsexplosion kommt. | Dies ist optional.        |
|**Ignorieren**     | Die ausgewählte Spalte wird ignoriert.        | Dies ist optional. Für Datenquellen, die die Verwendung einer Abfrage zum Abrufen von Daten unterstützen, ist keine Option „Ignorieren“ vorhanden.       |


:::image type="content" source="../media/schema-configuration.png" alt-text="Screenshot der Schemakonfiguration." lightbox="../media/schema-configuration.png":::

Wählen Sie nach dem Konfigurieren des Schemas die Option **Schema überprüfen** aus. Metrics Advisor führt die folgenden Überprüfungen aus:
- Ob der Zeitstempel der abgefragten Daten in ein einzelnes Intervall fällt. 
- Ob für dieselbe Dimensionskombination innerhalb eines Metrikintervalls doppelte Werte zurückgegeben werden.  

### <a name="automatic-roll-up-settings"></a>Einstellungen für automatisches Rollup

> [!IMPORTANT]
> Wenn Sie die Grundursachenanalyse und andere Diagnosefunktionen aktivieren möchten, konfigurieren Sie die Einstellungen für automatisches Rollup. Nachdem Sie die Analyse aktiviert haben, können Sie die Einstellungen für automatisches Rollup nicht mehr ändern.

Metrics Advisor kann während der Erfassung automatisch Aggregationen für jede Dimension ausführen. Anschließend erstellt der Dienst eine Hierarchie, die Sie bei der Grundursachenanalyse und anderen Diagnosefunktionen verwenden können. Weitere Informationen finden Sie unter [Einstellungen für automatisches Rollup](../how-tos/onboard-your-data.md#automatic-roll-up-settings).

Geben Sie dem Datenfeed einen benutzerdefinierten Namen, der dann in Ihrem Arbeitsbereich angezeigt wird. Klicken Sie auf **Submit** (Senden). 

## <a name="tune-detection-configuration"></a>Optimieren der Erkennungskonfiguration

Nach dem Hinzufügen des Datenfeeds wird von Metrics Advisor versucht, Metrikdaten des angegebenen Startdatums zu erfassen. Es dauert einige Zeit, bis die Daten vollständig erfasst wurden. Wählen Sie zum Anzeigen des Erfassungsstatus im oberen Bereich der Datenfeedseite die Option **Ingestion progress** (Erfassungsstatus) aus. Wenn Daten erfasst wurden, wendet Metrics Advisor die Erkennung an und überwacht die Quelle weiter auf neue Daten.

Wählen Sie nach der Anwendung der Erkennung eine der Metriken aus, die im Datenfeed aufgelistet sind, um zur Seite **Metrikdetails** zu gelangen. Dort haben Sie folgende Möglichkeiten: 
- Anzeigen von Visualisierungen aller Zeitreihensegmente unter der betreffenden Metrik.
- Aktualisieren der Erkennungskonfiguration, um die erwarteten Ergebnisse zu erhalten.
- Einrichten von Benachrichtigungen für erkannte Anomalien.

:::image type="content" source="../media/metric-details.png" alt-text="Screenshot der Metrikdetails." lightbox="../media/metric-details.png":::

## <a name="view-diagnostic-insights"></a>Anzeigen von Diagnoseerkenntnissen

Nach dem Optimieren der Erkennungskonfiguration sollten die erkannten Anomalien den tatsächlichen Anomalien in Ihren Daten entsprechen. Metrics Advisor führt eine Analyse mehrdimensionaler Metriken durch, um die Grundursache für eine bestimmte Dimension zu ermitteln. Der Dienst führt außerdem metrikübergreifende Analysen mithilfe des Features „Metrikgraph“ durch. 

Wählen Sie zum Anzeigen der Diagnoseerkenntnisse in Zeitreihenvisualisierungen die roten Punkte aus. Diese roten Punkte stehen für die erkannten Anomalien. Daraufhin wird ein Fenster mit einem Link zur Seite für die Incidentanalyse angezeigt. 

:::image type="content" source="../media/incident-link.png" alt-text="Screenshot eines Incidentlinks." lightbox="../media/incident-link.png":::

Auf der Seite „Incidentanalyse“ wird eine Gruppe verwandter Anomalien und Diagnoseerkenntnisse angezeigt. In den folgenden Abschnitten werden die wichtigsten Schritte zum Diagnostizieren eines Incidents beschrieben.

### <a name="check-the-summary-of-the-current-incident"></a>Überprüfen der Zusammenfassung des aktuellen Incidents

Die Zusammenfassung finden Sie ganz oben auf der Seite der Incidentanalyse. Diese Zusammenfassung enthält grundlegende Informationen, Aktionen und Ablaufverfolgungen sowie eine analysierte Grundursache. Es werden grundlegende Informationen für die Bereiche „Wichtigste betroffene Reihen“ (mit Diagramm), „Start-/Endzeit der Beeinträchtigung“, „Schweregrad“ und „Gesamte Anomalien“ angezeigt.

Bei der analysierten Grundursache handelt es sich um ein automatisch analysiertes Ergebnis. Von Metrics Advisor wird eine Analyse aller Anomalien durchgeführt, die für eine Zeitreihe innerhalb einer Metrik mit verschiedenen Dimensionswerten mit demselben Zeitstempel erfasst wurden. Anschließend führt der Dienst eine Korrelation durch, um verwandte Anomalien zu gruppieren, und es werden Informationen zur Grundursache generiert.

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="Screenshot einer Zusammenfassung der Incidentdiagnose." lightbox="../media/diagnostics/incident-summary.png":::

Basierend auf diesen Informationen können Sie sich bereits einen Überblick über den aktuellen Anomaliestatus sowie über die Auswirkungen des Incidents und die wahrscheinlichste Grundursache verschaffen. Sie können dann sofort Maßnahmen ergreifen, um den Incident zu beheben. 

### <a name="view-cross-dimension-diagnostic-insights"></a>Anzeigen von dimensionsübergreifenden Diagnoseerkenntnissen

Mithilfe des Diagnosestrukturfeatures können Sie auf ganzheitliche Weise auch ausführlichere Informationen zum Anomaliestatus für andere Dimensionen innerhalb derselben Metrik erhalten.

Bei Metriken mit mehreren Dimensionen wird die Zeitreihe von Metrics Advisor als Hierarchie kategorisiert (als „Diagnosestruktur“ bezeichnet). Es kann beispielsweise sein, dass die Metrik „Umsatz“ anhand von zwei Dimensionen überwacht wird: „Region“ und „Kategorie“. Sie benötigen einen aggregierten Dimensionswert wie `SUM`. Dann werden die Zeitreihen von `region = SUM` und `category = SUM` als Stammknoten der Struktur kategorisiert. Immer wenn unter der Dimension `SUM` eine Anomalie erfasst wird, können Sie sie analysieren, um zu ermitteln, welcher spezifische Dimensionswert am meisten zur Anomalie des übergeordneten Knotens beigetragen hat. Wählen Sie die einzelnen Knoten aus, um sie zu erweitern und so detaillierte Informationen anzuzeigen.

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="Screenshot, der die dimensionsübergreifende Ansicht der Incidentdiagnose zeigt." lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights"></a>Anzeigen metrikübergreifender Diagnoseerkenntnisse

Da es manchmal schwierig sein kann, ein Problem durch die Überprüfung des Anomaliestatus von nur einer Metrik zu analysieren, müssen Sie ggf. mehrere Metriken korrelieren. Hierzu können Kunden einen „Metrikgraphen“ konfigurieren, der die Beziehungen zwischen Metriken anzeigt. 

Mithilfe des im vorherigen Abschnitt beschriebenen dimensionsübergreifenden Diagnoseergebnisses können Sie feststellen, dass die Grundursache auf einen bestimmten Dimensionswert beschränkt ist. Verwenden Sie dann einen „Metrikgraphen“, um nach der Grundursache der analysierten Dimension zu filtern und um den Anomaliestatus für andere Metriken zu überprüfen.

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="Screenshot, der die metrikübergreifende Analyse der Incidentdiagnose zeigt." lightbox="../media/diagnostics/cross-metrics-analysis.png":::

Sie können auch mithilfe zusätzlicher Features über weitere Diagnoseerkenntnisse pivotieren. Mit diesen Features können Sie einen Drilldown in Dimensionen von Anomalien durchführen, ähnliche Anomalien anzeigen und metrikübergreifende Vergleiche durchführen. Weitere Informationen finden Sie unter [Diagnostizieren eines Incidents](../how-tos/diagnose-an-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Erhalten einer Benachrichtigung, wenn neue Anomalien gefunden werden

Wenn Sie im Falle einer erkannten Anomalie in Ihren Daten eine Benachrichtigung erhalten möchten, können Sie ein Abonnement für einzelne oder mehrere Ihrer Metriken erstellen. Metrics Advisor verwendet Hooks, um Warnungen zu senden. Drei Arten von Hooks werden unterstützt: E-Mail-Hook, Webhook und Azure DevOps. Hier wird als Beispiel ein Webhook verwendet. 

### <a name="create-a-web-hook"></a>Erstellen eines Webhooks

In Metrics Advisor können Sie einen Webhook verwenden, um eine Anomalie programmgesteuert aufzufinden. Der Dienst ruft eine benutzerseitig bereitgestellte API auf, wenn eine Warnung ausgelöst wird. Weitere Informationen finden Sie unter [Erstellen eines Hooks](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Konfigurieren der Warnungseinstellungen

Nach Erstellung eines Hooks wird durch eine Warnungseinstellung bestimmt, wie und welche Warnbenachrichtigungen gesendet werden. Pro Metrik können jeweils mehrere Warnungseinstellungen festgelegt werden. Zwei wichtige Einstellungen sind **Warnung für** zur Angabe der einzuschließenden Anomalien und **Filter anomaly options** (Anomalieoptionen filtern), um zu definieren, welche Anomalien in die Warnung eingeschlossen werden sollen. Weitere Informationen finden Sie unter [Hinzufügen oder Bearbeiten von Warnungseinstellungen](../how-tos/alerts.md#add-or-edit-alert-settings).


## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen Ihrer Metrikdaten zu Metrics Advisor](../how-tos/onboard-your-data.md)
    - [Verwalten Ihrer Datenfeeds](../how-tos/manage-data-feeds.md)
    - [Verbinden verschiedener Datenquellen](../data-feeds-from-different-sources.md)
- [Verwenden der Clientbibliotheken oder REST-APIs zum Anpassen Ihrer Lösung](./rest-api-and-client-library.md)
- [Konfigurieren von Metriken und Optimieren der Erkennungskonfiguration](../how-tos/configure-metrics.md)
