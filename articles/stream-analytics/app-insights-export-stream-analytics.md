---
title: Exportieren aus Azure Application Insights mithilfe von Stream Analytics | Microsoft-Dokumentation
description: Stream Analytics kann aus Application Insights exportierte Daten fortlaufend transformieren, filtern und weiterleiten.
ms.topic: conceptual
ms.date: 01/08/2019
ms.service: stream-analytics
ms.openlocfilehash: 56ce47f232c78637b82c16501e47f2bff8707430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598512"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Verwenden von Stream Analytics zum Verarbeiten von Daten, die aus Application Insights exportiert wurden

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ist das ideale Tool für die Verarbeitung von Daten, die [aus Application Insights exportiert wurden](../azure-monitor/app/export-telemetry.md). Stream Analytics kann Daten aus einer Vielzahl von Quellen abrufen. Das Tool kann die Daten transformieren und filtern und anschließend an eine Vielzahl von Senken weiterleiten.

In diesem Beispiel wird ein Adapter erstellt, der Daten aus Application Insights mittels fortlaufendem Export abruft, einige Felder umbenennt und bearbeitet sowie an Power BI weiterreicht.

> [!WARNING]
> Es gibt wesentlich bessere und einfachere [empfohlene Möglichkeiten, Application Insights-Daten in Power BI anzuzeigen](../azure-monitor/app/export-power-bi.md). Die hier beschriebene Möglichkeit ist lediglich ein Beispiel zum Veranschaulichen, wie exportierte Daten verarbeitet werden.

> [!IMPORTANT]
> Der fortlaufende Export ist veraltet und wird nur für klassische Application Insights-Ressourcen unterstützt. [Migrieren Sie zu einer arbeitsbereichsbasierten Application Insights-Ressource](../azure-monitor/app/convert-classic-resource.md), um [Diagnoseeinstellungen](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export) zum Exportieren von Telemetriedaten zu verwenden.


![Blockdiagramm für den Export über Stream Analytics nach PowerBI.](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Erstellen von Speicher in Azure
Durch fortlaufende Exportaktivitäten werden Daten an ein Azure-Speicherkonto übertragen, daher müssen Sie zuerst Speicher erstellen.

1. Erstellen Sie ein „klassisches“ Speicherkonto in Ihrem Abonnement im [Azure-Portal](https://portal.azure.com).
   
   ![Screenshot des Azure-Portals: Wählen Sie Neu, Daten, Speicher aus.](./media/app-insights-export-stream-analytics/030.png)
2. Erstellen eines Containers
   
    ![Screenshot des neuen Speichers: Wählen Sie „Container“ aus, klicken Sie auf die Schaltfläche „Container“ und anschließend auf „Hinzufügen“.](./media/app-insights-export-stream-analytics/040.png)
3. Kopieren Sie den Speicherzugriffsschlüssel.
   
    Sie benötigen ihn bald, um die Eingabe für den Stream Analytics-Dienst einzurichten.
   
    ![Screenshot des Speichers: Öffnen Sie „Einstellungen“, „Schlüssel“, und kopieren Sie den primären Zugriffsschlüssel.](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starten des fortlaufenden Exports in den Azure-Speicher

[fortlaufenden Export](../azure-monitor/app/export-telemetry.md) werden Daten aus Application Insights in den Azure-Speicher verschoben. 

1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.
   
    ![Screenshot aller Ressourcen im Azure-Portal: Wählen Sie Application Insights aus und dann Ihre Anwendung.](./media/app-insights-export-stream-analytics/050.png)
2. Erstellen Sie einen fortlaufenden Export.
   
    ![Screenshot von Application Insights. Wählen Sie Einstellungen, fortlaufender Export und dann Hinzufügen.](./media/app-insights-export-stream-analytics/060.png)

    Wählen Sie das zuvor erstellte Speicherkonto aus:

    ![Screenshot: Fortlaufender Export, Auswählen mit Hinzufügen und legen Sie dann fest, wohin exportiert werden soll.](./media/app-insights-export-stream-analytics/070.png)

    Legen Sie die Ereignistypen fest, die Sie anzeigen möchten:

    ![Screenshot: Hinzufügen von fortlaufendem Export und Auswählen von Ereignistypen.](./media/app-insights-export-stream-analytics/080.png)

1. Warten Sie, bis sich einige Daten angesammelt haben. Lehnen Sie sich zurück, und lassen Sie Ihre Benutzer die Anwendung eine Weile lang verwenden. Telemetriedaten gehen ein, und Sie sehen statistische Diagramme im [Metrik-Explorer](../azure-monitor/essentials/metrics-charts.md) sowie einzelne Ereignisse in der [Diagnosesuche](../azure-monitor/app/diagnostic-search.md).
   
    Darüber hinaus werden die Daten in Ihren Speicher exportiert. 
2. Überprüfen Sie die exportierten Daten. Wählen Sie in Visual Studio **Anzeigen/Cloud Explorer**, und öffnen Sie „Azure/Storage“. (Wenn diese Menüoption nicht verfügbar ist, müssen Sie das Azure SDK installieren: Öffnen Sie das Dialogfeld „Neues Projekt“ und anschließend „Visual C#/Cloud/Microsoft Azure SDK für .NET abrufen“.)
   
    ![Screenshot, der zeigt, wie Sie die Ereignistypen festlegen, die Sie anzeigen möchten](./media/app-insights-export-stream-analytics/04-data.png)
   
    Notieren Sie sich den gemeinsamen Teil des Pfadnamens, der sich vom Anwendungsnamen und vom Instrumentierungsschlüssel ableitet.

Die Ereignisse werden in Blobdateien im JSON-Format geschrieben. Jede Datei kann ein oder mehrere Ereignisse enthalten. Daher möchten wir die Ereignisdaten lesen und die gewünschten Felder herausfiltern. Es gibt viele verschiedene Möglichkeiten zur Nutzung der Daten, aber unser Plan besteht darin, die Daten mit Stream Analytics per Pipe an Power BI weiterzureichen.

## <a name="create-an-azure-stream-analytics-instance"></a>Erstellen einer Azure Stream Analytics-Instanz

Wählen Sie im [Azure-Portal](https://portal.azure.com/) den Azure Stream Analytics-Dienst aus, und erstellen Sie einen neuen Stream Analytics-Auftrag:

![Screenshot, der die Hauptseite zum Erstellen eines Stream Analytics-Auftrags im Azure-Portal zeigt](./media/app-insights-export-stream-analytics/001.png)

![Screenshot, der die erforderlichen Details beim Erstellen eines neuen Stream Analytics-Auftrags zeigt](./media/app-insights-export-stream-analytics/002.png)

Wählen Sie nach dem Erstellen des neuen Auftrags die Option **Zu Ressource wechseln** aus.

![Screenshot, der die Meldung anzeigt, die empfangen wird, wenn die Bereitstellung des neuen Stream Analytics-Auftrags erfolgreich war](./media/app-insights-export-stream-analytics/003.png)

### <a name="add-a-new-input"></a>Hinzufügen einer neuen Eingabe

![Screenshot, der zeigt, wie dem Stream Analytics-Auftrag Eingaben hinzugefügt werden](./media/app-insights-export-stream-analytics/004.png)

Legen Sie den Auftrag so fest, dass er Eingaben vom Blob für den fortlaufenden Export erhält:

![Screenshot, der die Konfiguration des Stream Analytics-Auftrags zum Übernehmen von Eingaben aus einem fortlaufenden Exportblob veranschaulicht](./media/app-insights-export-stream-analytics/0005.png)

Jetzt benötigen Sie den primären Zugriffsschlüssel aus Ihrem Speicherkonto, den Sie zuvor notiert haben. Legen Sie diesen als Speicherkontoschlüssel fest.

### <a name="set-path-prefix-pattern"></a>Festlegen des Präfixmusters des Pfads

**Achten Sie darauf, dass das Datum das Format „JJJJ-MM-TT“ (mit Bindestrichen) aufweist.**

Das Präfixmuster des Pfads gibt an, wo Stream Analytics die Eingabedateien im Speicher findet. Sie müssen es so einstellen, dass es der Speicherung der Daten durch den fortlaufenden Export entspricht. Legen Sie ihn wie folgt fest:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

In diesem Beispiel:

* `webapplication27` ist der Name der Application Insights-Ressource in **Kleinbuchstaben**.
* `1234...` ist der Instrumentierungsschlüssel der Application Insights-Ressource **ohne Bindestriche**. 
* `PageViews` ist der zu analysierende Datentyp. Die verfügbaren Typen sind abhängig von dem Filter, den Sie im fortlaufenden Export festlegen. Untersuchen Sie die exportierten Daten, um die anderen verfügbaren Typen anzuzeigen, und sehen Sie sich das [Exportdatenmodell](../azure-monitor/app/export-data-model.md)an.
* `/{date}/{time}` ist ein als Literal geschriebenes Muster.

> [!NOTE]
> Überprüfen Sie den Speicher, um sicherzustellen, dass der Pfad stimmt.

## <a name="add-new-output"></a>Hinzufügen einer neuen Ausgabe

Wählen Sie Ihren Auftrag und dann **Ausgaben** > **Hinzufügen** aus.

![Screenshot, der das Auswählen des Stream Analytics-Auftrags zum Hinzufügen einer neuen Ausgabe zeigt](./media/app-insights-export-stream-analytics/006.png)


![Screenshot der neuen Ausgabe, Auswählen des neuen Kanals, Ausgaben, Hinzufügen und dann Power BI.](./media/app-insights-export-stream-analytics/010.png)

Geben Sie Ihr **Geschäfts- oder Schulkonto** an, um Stream Analytics für den Zugriff auf Ihre Power BI-Ressource zu autorisieren. Geben Sie anschließend Namen für die Ausgabe sowie für den Power BI-Zieldatensatz und die Zieltabelle an.

## <a name="set-the-query"></a>Festlegen der Abfrage

Die Abfrage bestimmt die Übersetzung von der Eingabe zur Ausgabe.

Überprüfen Sie mithilfe der Testfunktion, ob Sie die richtige Ausgabe erhalten. Geben Sie die Beispieldaten an, die Sie der Eingabeseite entnommen haben.

### <a name="query-to-display-counts-of-events"></a>Abfrage zum Anzeigen der Anzahl von Ereignissen

Fügen Sie diese Abfrage ein:

```SQL
SELECT
  flat.ArrayValue.name,
  count(*)
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.[event]) as flat
GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input ist der Alias, den wir der Datenstromeingabe gegeben haben.
* „pbi-outout“ ist der definierte Ausgabealias.
* Wir verwenden [OUTER APPLY GetElements](/stream-analytics-query/apply-azure-stream-analytics), weil sich der Ereignisname in einem geschachtelten JSON-Array befindet. Die SELECT-Anweisung wählt dann den Ereignisnamen zusammen mit der Anzahl der Instanzen mit diesem Namen im angegebenen Zeitraum aus. Die [Group By](/stream-analytics-query/group-by-azure-stream-analytics)-Klausel gruppiert die Elemente in Zeiträume von einer Minute.

### <a name="query-to-display-metric-values"></a>Abfrage zum Anzeigen metrischer Werte

```SQL
SELECT
  A.context.data.eventtime,
  avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.context.custom.metrics) as flat
GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime
```

* Diese Abfrage führt einen Drilldown in die Telemetrie der Metriken durch, um die Uhrzeit und den metrischen Wert des Ereignisses abzurufen. Die metrischen Werte befinden sich in einem Array, daher verwenden wir das Muster „OUTER APPLY GetElements“ zum Extrahieren der Zeilen. In diesem Fall lautet der Name der Metrik „myMetric“.

### <a name="query-to-include-values-of-dimension-properties"></a>Abfrage zum Anzeigen von Dimensionseigenschaften

```SQL
WITH flat AS (
SELECT
  MySource.context.data.eventTime as eventTime,
  InstanceId = MyDimension.ArrayValue.InstanceId.value,
  BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
FROM MySource
OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
)
SELECT
  eventTime,
  InstanceId,
  BusinessUnitId
INTO AIOutput
FROM flat
```

* Diese Abfrage enthält Dimensionseigenschaftswerte, ohne dabei davon abhängig zu sein, dass eine bestimmte Dimension in einem festen Index im Dimensionsarray liegt.

## <a name="run-the-job"></a>Ausführung des Auftrags.

Sie können ein Datum in der Vergangenheit auswählen, ab dem der Auftrag ausgeführt werden soll.

![Screenshot von Stream Analyics: Wählen Sie den Auftrag aus und klicken Sie auf Abfrage. Fügen Sie das folgenden Beispiel ein.](./media/app-insights-export-stream-analytics/008.png)

Warten Sie, bis der Auftrag ausgeführt wird.

## <a name="see-results-in-power-bi"></a>Anzeigen der Ergebnisse in Power BI

> [!WARNING]
> Es gibt wesentlich bessere und einfachere [empfohlene Möglichkeiten, Application Insights-Daten in Power BI anzuzeigen](../azure-monitor/app/export-power-bi.md). Die hier beschriebene Möglichkeit ist lediglich ein Beispiel zum Veranschaulichen, wie exportierte Daten verarbeitet werden.


Öffnen Sie Power BI mit Ihrem Geschäfts- oder Schulkonto, und wählen Sie das Dataset und die Tabelle aus, die Sie als Ausgabe des Stream Analytics-Auftrags definiert haben.

![Screenshot von Power BI: Wählen Sie Ihr Dataset und die Felder aus.](./media/app-insights-export-stream-analytics/200.png)

Jetzt können Sie dieses Dataset in Berichten und Dashboards in [Power BI](https://powerbi.microsoft.com)verwenden.

![Screenshot eines Berichtbeispiels, das anhand eines Datasets in Power BI erstellt wurde.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Sie sehen keine Daten?

* Überprüfen Sie, ob das [Datumsformat](#set-path-prefix-pattern) ordnungsgemäß auf JJJJ-MM-TT (mit Bindestrichen) festgelegt ist.

## <a name="next-steps"></a>Nächste Schritte
* [Fortlaufendem Export](../azure-monitor/app/export-telemetry.md)
* [Detaillierte Datenmodellreferenz für die Eigenschaftstypen und -werte.](../azure-monitor/app/export-data-model.md)
* [Application Insights](../azure-monitor/app/app-insights-overview.md)
