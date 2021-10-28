---
title: Fortlaufender Export von Telemetriedaten aus Application Insights | Microsoft Docs
description: Exportieren Sie Diagnose- und Nutzungsdaten in Microsoft Azure-Speicher, die Sie anschließendes daraus herunterladen.
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: 1d4a9a6e60d39b01ea1ebe99289c9bb3a1c50c2b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233524"
---
# <a name="export-telemetry-from-application-insights"></a>Exportieren von Telemetriedaten aus Application Insights
Möchten Sie Ihre Telemetriedaten länger aufbewahren als von der standardmäßigen Beibehaltungsdauer vorgesehen? Oder möchten Sie sie in einer speziellen Art und Weise verarbeiten? Der fortlaufende Export eignet sich hierfür ideal. Die Ereignisse, die Sie im Application Insights-Portal sehen, können im JSON-Format in Microsoft Azure-Speicher exportiert werden. Sie können Ihre Daten anschließend herunterladen und den Code schreiben, den Sie zu ihrer Verarbeitung benötigen.  

> [!IMPORTANT]
> Der fortlaufende Export ist veraltet. [Migrieren Sie zu einer arbeitsbereichsbasierten Application Insights-Ressource](convert-classic-resource.md), um [Diagnoseeinstellungen](#diagnostic-settings-based-export) zum Exportieren von Telemetriedaten zu verwenden.

> [!NOTE]
> Der fortlaufende Export wird nur für klassische Application Insights Ressourcen unterstützt. [Für arbeitsbereichsbasierte Application Insights-Ressourcen](./create-workspace-resource.md) müssen [Diagnoseeinstellungen](./create-workspace-resource.md#export-telemetry) verwendet werden.
>

Bevor Sie den fortlaufenden Export einrichten, sollten Sie folgende Alternativen zu dieser Vorgehensweise prüfen:

* Mithilfe der Schaltfläche „Exportieren“ oben auf den Registerkarten „Metriken“ oder „Suche“ können Sie Tabellen und Diagramme in ein Excel-Arbeitsblatt übertragen.

* [Analytics](../logs/log-query-overview.md) bietet eine leistungsstarke Abfragesprache für Telemetriedaten. Das Exportieren von Ergebnissen ist auch möglich.
* Wenn Sie [Ihre Daten in Power BI untersuchen](./export-power-bi.md)möchten, benötigen Sie dazu keinen fortlaufenden Export.
* Mit dem [REST-API für Datenzugriff](https://dev.applicationinsights.io/) können Sie programmgesteuert auf Ihre Telemetriedaten zugreifen.
* Sie können auch auf das Setup für den [fortlaufenden Export über PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport) zugreifen.

Nachdem Ihre Daten vom Fortlaufende Export in den Speicher kopiert wurden (wo sie beliebig lange bleiben können), sind sie für die übliche [Aufbewahrungsdauer](./data-retention-privacy.md)auch noch in Application Insights verfügbar.

## <a name="supported-regions"></a>Unterstützte Regionen

Der fortlaufende Export wird in den folgenden Regionen unterstützt:

* Asien, Südosten
* Kanada, Mitte
* Indien, Mitte
* Nordeuropa
* UK, Süden
* Australien (Osten)
* Japan, Osten
* Korea, Mitte
* Frankreich, Mitte
* Asien, Osten
* USA (Westen)
* USA (Mitte)
* USA (Ost) 2
* USA Süd Mitte
* USA, Westen 2
* Südafrika, Norden
* USA Nord Mitte
* Brasilien, Süden
* Schweiz, Norden
* Australien, Südosten
* UK, Westen
* Deutschland, Westen-Mitte
* Schweiz, Westen
* Australien, Mitte 2
* VAE, Mitte
* Brasilien, Südosten
* Australien, Mitte
* Vereinigte Arabische Emirate, Norden
* Norwegen, Osten
* Japan, Westen

> [!NOTE]
> Der fortlaufende Export funktioniert weiterhin für Anwendungen in den Regionen **USA, Osten** und **Europa, Westen**, wenn der Export vor dem 23. Februar 2021 konfiguriert wurde. Für Anwendungen in den Regionen **USA, Osten** oder **Europa, Westen** können keine neuen Regeln für den fortlaufenden Export konfiguriert werden, unabhängig davon, wann die Anwendung erstellt wurde.

## <a name="continuous-export-advanced-storage-configuration"></a>Fortlaufender Export – erweiterte Speicherkonfiguration

Der fortlaufende Export **unterstützt nicht** die folgenden Azure-Speicherfeatures/-Konfigurationen:

* Verwenden von [VNET-/Azure Storage-Firewalls](../../storage/common/storage-network-security.md) in Verbindung mit Azure Blob Storage.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Erstellen eines fortlaufenden Exports

> [!NOTE]
> Eine Anwendung kann nicht mehr als 3 TB Daten pro Tag exportieren. Wenn mehr als 3 TB pro Tag exportiert werden, wird der Export deaktiviert. Um ohne Beschränkung zu exportieren, verwenden Sie den [auf Diagnoseeinstellungen basierenden Export](#diagnostic-settings-based-export).

1. Öffnen Sie in der Application Insights-Ressource für Ihre App im Konfigurationsbereich rechts die Option „Fortlaufender Export“, und wählen Sie **Hinzufügen** aus:

2. Wählen Sie die Telemetriedatentypen aus, die Sie exportieren möchten.

3. Erstellen oder wählen Sie ein [Azure-Speicherkonto](../../storage/common/storage-introduction.md), in dem Sie die Daten speichern möchten. Weitere Informationen zu Preisoptionen für Speicher finden Sie auf der [Seite mit den offiziellen Preisen](https://azure.microsoft.com/pricing/details/storage/).

     Klicken Sie auf „Hinzufügen“, „Exportziel“, „Speicherkonto“, und erstellen Sie dann einen neuen Speicher, oder wählen Sie einen vorhandenen Speicher aus.

    > [!Warning]
    > Der Speicherort wird standardmäßig auf die gleiche geografische Region festgelegt, in der sich auch Ihre Application Insights-Ressource befindet. Wenn Sie die Daten in einer anderen Region speichern, fallen möglicherweise Übertragungsgebühren an.

4. Erstellen Sie einen Container im Speicher, oder wählen Sie einen aus.

> [!NOTE]
> Nach der Erstellung Ihres Exports werden neu erfasste Daten an Azure Blob Storage übertragen. Beim fortlaufenden Export werden nur neue Telemetriedaten übertragen, die nach der Aktivierung des fortlaufenden Exports erstellt/erfasst werden. Daten, die bereits vor der Aktivierung des fortlaufenden Exports vorhanden waren, werden nicht exportiert, und es gibt keine Möglichkeit, zuvor erstellte Daten rückwirkend mithilfe des fortlaufenden Exports zu exportieren.

Es kann etwa eine Stunde dauern, bis Daten im Speicher angezeigt werden.

Nach Abschluss des ersten Exports wird in Ihrem Azure Blob Storage-Container eine Struktur ähnlich der folgenden angezeigt: (Diese hängt von den Daten ab, die Sie erfassen.)

|Name | BESCHREIBUNG |
|:----|:------|
| [Verfügbarkeit](export-data-model.md#availability) | Liefert Berichtdaten zu [Verfügbarkeitswebtests](./monitor-web-app-availability.md).  |
| [Event](export-data-model.md#events) | Von [TrackEvent()](./api-custom-events-metrics.md#trackevent)generierte benutzerdefinierte Ereignisse. 
| [Ausnahmen](export-data-model.md#exceptions) |Melden [Ausnahmen](./asp-net-exceptions.md) auf dem Server und im Browser.
| [Meldungen](export-data-model.md#trace-messages) | Gesendet von [TrackTrace](./api-custom-events-metrics.md#tracktrace) und über die [Protokollierungsadapter](./asp-net-trace-logs.md).
| [Metriken](export-data-model.md#metrics) | Generiert durch Metrik-API-Aufrufe
| [PerformanceCounters](export-data-model.md) | Von Application Insights erfasste Leistungsindikatoren
| [Anforderungen](export-data-model.md#requests)| Gesendet von [TrackRequest](./api-custom-events-metrics.md#trackrequest). Die Standardmodule verwenden TrackRequest zum Berichten der Serverantwortzeit, gemessen auf dem Server.| 

### <a name="to-edit-continuous-export"></a>Bearbeiten des fortlaufenden Exports

Klicken Sie auf „Fortlaufender Export“, und wählen Sie das zu bearbeitende Speicherkonto aus.

### <a name="to-stop-continuous-export"></a>Beenden des fortlaufenden Exports

Um den Export zu beenden, klicken Sie auf „Deaktivieren“. Wenn Sie erneut auf „Aktivieren“ klicken, wird der Export mit neuen Daten neu gestartet. Sie erhalten nicht die Daten, die im Portal eingegangen sind, während der Export deaktiviert war.

Um den Export dauerhaft zu beenden, löschen Sie ihn. Dabei werden Ihre Daten nicht aus dem Speicher gelöscht.

### <a name="cant-add-or-change-an-export"></a>Wer darf einen Export ergänzen oder ändern?
* Zum Ergänzen oder Ändern von Exporten benötigen Sie das Zugriffsrecht „Besitzer“, „Mitwirkender“ oder „Application Insights-Mitwirkender“. [Weitere Informationen zu Rollen][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Welche Ereignisse werden abgerufen?
Bei den exportierten Daten handelt es sich um die Telemetrierohdaten, die Sie von Ihrer Anwendung empfangen. Wir fügen allerdings Standortdaten hinzu, die wir anhand der Client-IP-Adresse berechnen.

Daten, die während der [Stichprobenerstellung](./sampling.md) verworfen wurden, werden nicht in die exportierten Daten aufgenommen.

Andere berechnete Metriken sind nicht enthalten. Wir exportieren z. B. nicht die durchschnittliche CPU-Auslastung, doch wir exportieren die rohen Telemetriedaten, anhand derer der Durchschnitt berechnet wird.

Die Daten umfassen außerdem die Ergebnisse von [Verfügbarkeitswebtests](./monitor-web-app-availability.md), die Sie eingerichtet haben.

> [!NOTE]
> **Stichproben** Wenn Ihre Anwendung große Datenmengen sendet, wird möglicherweise das Stichprobenfeature verwendet und sendet nur einen Teil der Telemetriedaten. [Erfahren Sie mehr über das Erstellen von Stichproben.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Untersuchen der Daten
Sie können den Speicher direkt im Portal überprüfen. Klicken Sie im Menü links oben auf „Startseite“, und wählen Sie im oberen Bereich unter „Azure-Dienste“ **Speicherkonten** aus. Wählen Sie den Namen des Speicherkontos, anschließend auf der Übersichtsseite unter den Diensten **Blobs** und schließlich den Containernamen aus.

Um Azure Storage in Visual Studio zu überprüfen, öffnen Sie **Anzeigen** und dann **Cloud-Explorer**. (Wenn dieser Menübefehl nicht verfügbar ist, müssen Sie das Azure SDK installieren: Öffnen Sie das Dialogfeld **Neues Projekt**, erweitern Sie „Visual C#/Cloud“, und wählen Sie dann **Microsoft Azure SDK für .NET abrufen** aus.)

Beim Öffnen Ihres Blobspeichers sehen Sie einen Container mit einer Gruppe von Blobdateien. Der URI der einzelnen Dateien wird vom Application Insights-Ressourcennamen, dem zugehörigen Instrumentierungsschlüssel sowie von Typ, Datum und Uhrzeit der Telemetriedaten abgeleitet. (Der Ressourcenname enthält nur Kleinbuchstaben, und beim Instrumentierungsschlüssel werden Bindestriche entfernt.)

![Überprüfen Sie den Blobspeicher mit einem geeigneten Tool](./media/export-telemetry/04-data.png)

Datum und Uhrzeit werden in UTC angegeben und entsprechen dem Zeitpunkt, an dem die Telemetriedaten im Speicher abgelegt wurden, nicht dem Zeitpunkt ihrer Erzeugung. Wenn Sie also Code zum Herunterladen der Daten schreiben, kann sich dieser linear durch die Daten bewegen.

Hier ist die Form des Pfads:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Hierbei gilt:

* `blobCreationTimeUtc` ist die Uhrzeit, zu der das Blob im internen Stagingspeicher erstellt wurde.
* `blobDeliveryTimeUtc` ist die Uhrzeit, zu der das Blob in den Zielspeicher für den Export kopiert wird.

## <a name="data-format"></a><a name="format"></a> Datenformat
* Jedes Blob ist eine Textdatei, die mehrere durch '\n' getrennte Zeilen enthält. Es enthält die Telemetriedaten, die über einen Zeitraum von etwa einer halben Minute verarbeitet wurden.
* Jede Zeile stellt einen Telemetriedatenpunkt dar, z.B. eine Anforderung oder einen Seitenaufruf.
* Jede Zeile ist ein unformatiertes JSON-Dokument. Wenn Sie die Zeilen anzeigen möchten, öffnen Sie das Blob in Visual Studio, und wählen Sie **Bearbeiten** > **Erweitert** > **Formatdatei** aus:

   ![Zeigen Sie die Telemetriedaten mit einem geeigneten Tool an](./media/export-telemetry/06-json.png)

Zeiten werden mithilfe von Teilstrichen dargestellt: 10.000 Teilstriche = 1 ms. Diese Werte zeigen beispielsweise eine Dauer von 1 ms für das Senden einer Anforderung aus dem Browser, von 3 ms für den Empfang und von 1,8 s für die Verarbeitung der Seite im Browser an:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Detaillierte Datenmodellreferenz für die Eigenschaftstypen und -werte.](export-data-model.md)

## <a name="processing-the-data"></a>Verarbeiten der Daten
Im kleinen Rahmen können Sie Code zum Aufteilen Ihrer Daten schreiben, sie in eine Kalkulationstabelle einlesen usw. Beispiel:

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

Ein umfangreicheres Codebeispiel finden Sie unter [Verwenden einer Workerrolle][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Löschen der alten Daten
Sie sind für die Verwaltung Ihrer Speicherkapazität und das Löschen der alten Daten nach Bedarf verantwortlich.

## <a name="if-you-regenerate-your-storage-key"></a>Wenn Sie Ihren Speicherschlüssel erneut generieren...
Wenn Sie den Schlüssel zu Ihrem Speicher ändern, funktioniert der fortlaufende Export nicht mehr. In Ihrem Azure-Konto wird eine Benachrichtigung angezeigt.

Öffnen Sie die Registerkarte „Fortlaufender Export“, und bearbeiten Sie den Export. Bearbeiten Sie das Exportziel, aber lassen Sie den gleichen Speicher ausgewählt. Klicken Sie auf "OK", um zu bestätigen.

Der fortlaufende Export wird neu gestartet.

## <a name="export-samples"></a>Export-Beispiele

* [Exportieren in SQL mit Stream Analytics][exportasa]
* [Stream Analytics-Beispiel 2](../../stream-analytics/app-insights-export-stream-analytics.md)

Bei größeren Dimensionen sollten Sie [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-Cluster in der Cloud erwägen. HDInsight bietet eine Vielzahl von Technologien zum Verwalten und Analysieren von Big Data, die zum Verarbeiten von Daten genutzt werden können, die von Application Insights exportiert wurden.

## <a name="q--a"></a>Fragen und Antworten
* *Ich möchte aber nur ein Diagramm einmalig herunterladen.*  

    Das ist kein Problem. Klicken Sie oben auf der Registerkarte auf **Daten exportieren**.
* *Ich habe einen Export eingerichtet, aber es sind keine Daten im meinem Speicher.*

    Hat Application Insights Telemetriedaten aus Ihrer App empfangen, seit Sie den Export eingerichtet haben? Sie erhalten nur neue Daten.
* *Ich habe versucht, einen Export einrichten, aber der Zugriff wurde verweigert.*

    Wenn das Konto im Besitz Ihrer Organisation ist, müssen Sie der Gruppe "Besitzer" oder "Mitwirkende" angehören.
* *Kann ich Daten direkt in meinen eigenen lokalen Datenspeicher exportieren?*

    Nein, leider nicht. Unsere Export-Engine funktioniert zurzeit nur mit Azure-Speicher.  
* *Gibt es Begrenzung der Menge von Daten, die im Speicher abgelegt werden können?*

    Nein. Wir übertragen Daten immer weiter, bis Sie den Export löschen. Wir hören auf, wenn wir uns den Grenzwerten Ihres Blobspeichers nähern, der aber ziemlich groß ist. Es liegt an Ihnen, wie viel Speicher Sie belegen.  
* *Wie viele Blobs sollten im Speicher angezeigt werden?*

  * Für jeden Datentyp, den Sie zum Exportieren ausgewählt haben, wird jede Minute ein neues Blob erstellt (falls Daten verfügbar sind).
  * Darüber hinaus werden weitere Partitionseinheiten für Anwendungen mit hohem Datenverkehr zugeordnet. In diesem Fall erstellt jede Einheit jede Minute ein Blob.
* *Ich habe den Schlüssel zu meinem Speicher neu generiert oder den Namen des Containers geändert, und jetzt funktioniert der Export nicht mehr.*

    Bearbeiten Sie den Export, und öffnen Sie die Registerkarte „Exportziel“. Lassen Sie denselben Speicher wie zuvor ausgewählt, und klicken Sie auf "OK", um zu bestätigen. Der Export wird neu gestartet. Wenn die Änderung in den vergangenen Tagen erfolgt ist, gehen keine Daten verloren.
* *Kann ich den Export anhalten?*

    Ja. Klicken Sie auf „Deaktivieren“.

## <a name="code-samples"></a>Codebeispiele

* [Stream Analytics-Beispiel](../../stream-analytics/app-insights-export-stream-analytics.md)
* [Exportieren in SQL mit Stream Analytics][exportasa]
* [Detaillierte Datenmodellreferenz für die Eigenschaftstypen und -werte.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Auf Diagnoseeinstellungen basierender Export

Bei dem auf Diagnoseeinstellungen basierenden Export wird ein anderes Schema als beim fortlaufenden Export verwendet. Außerdem werden Features unterstützt, die vom fortlaufenden Export nicht unterstützt werden. Dies sind:

* Azure-Speicherkonten mit VNET, Firewalls und privaten Links
* Export in Event Hub

Führen Sie die folgenden Schritte aus, um zu dem auf Diagnoseeinstellungen basierender Export zu migrieren:

1. Deaktivieren Sie den aktuellen fortlaufenden Export.
2. [Migrieren Sie die Anwendung zu einer arbeitsbereichsbasierten Ressource](convert-classic-resource.md).
3. [Aktivieren Sie den Export von Diagnoseeinstellungen](create-workspace-resource.md#export-telemetry). Wählen Sie in der Application Insights-Ressource **Diagnoseeinstellungen > Diagnoseeinstellung hinzufügen** aus.

<!--Link references-->

[exportasa]: ../../stream-analytics/app-insights-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md