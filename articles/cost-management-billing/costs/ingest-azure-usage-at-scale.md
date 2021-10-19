---
title: Wiederholtes Abrufen umfangreicher Kostendatasets mit Exporten aus Azure Cost Management
description: Dieser Artikel unterstützt Sie beim regelmäßigen Abrufen großer Datenmengen mithilfe von Exporten aus Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a27e64e7b5c94d033529c08ea02e757ba8857549
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705842"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Regelmäßiges Abrufen großer Datasets mithilfe von Exporten

Dieser Artikel unterstützt Sie beim regelmäßigen Abrufen großer Datenmengen mithilfe von Exporten aus Cost Management. Exportieren ist die empfohlene Abrufmethode für nicht aggregierte Kostendaten. Das gilt insbesondere, wenn Nutzungsdateien aufgrund ihrer Größe nicht zuverlässig mithilfe der Nutzungsdetails-API aufgerufen und heruntergeladen werden können. Die exportierten Daten werden im von Ihnen gewählten Azure Storage-Konto platziert. Von dort können Sie die Daten in Ihre eigenen Systeme laden und nach Bedarf analysieren. Informationen zum Konfigurieren von Exporten im Azure-Portal finden Sie unter [Exportieren von Daten](tutorial-export-acm-data.md).

Wenn Sie Exporte in verschiedenen Bereichen automatisieren möchten, ist die exemplarische API-Anforderung aus dem nächsten Abschnitt ein guter Ausgangspunkt. Mithilfe der API für Exporte können Sie automatische Exporte als Teil Ihrer allgemeinen Umgebungskonfiguration erstellen. Automatische Exporte können dazu beitragen, dass Sie über die Daten verfügen, die Sie benötigen. Sie können in den Systemen Ihrer eigenen Organisation verwendet werden, während Sie Ihre Azure-Nutzung ausweiten.

## <a name="common-export-configurations"></a>Gängige Exportkonfigurationen

Machen Sie sich vor der Erstellung Ihres ersten Exports Gedanken zu Ihrem Szenario, und überlegen Sie sich, welche Konfigurationsoptionen dafür erforderlich sind. Folgende Exportoptionen stehen zur Verfügung:

- **Wiederholung**: Bestimmt, wie oft der Exportauftrag ausgeführt und wann eine Datei in Ihrem Azure Storage-Konto platziert wird. Zur Auswahl stehen „Täglich“, „Wöchentlich“ und „Monatlich“. Stimmen Sie die Wiederholungskonfiguration möglichst auf die Datenimportaufträge ab, die vom internen System Ihrer Organisation verwendet werden.
- **Wiederholungszeitraum**: Bestimmt die Gültigkeitsdauer des Exports. Dateien werden nur während des Wiederholungszeitraums exportiert.
- **Zeitrahmen**: Bestimmt die Datenmenge, die im Rahmen einer Ausführung durch den Export generiert wird. Gängige Optionen sind „MonthToDate“ und „WeekToDate“.
- **Startdatum**: Dient zum Konfigurieren des Beginns des Exportzeitplans. Ein Export wird zum Startdatum (StartDate) und anschließend auf der Grundlage der Wiederholung erstellt.
- **Typ**: Es gibt drei Exporttypen:
  - „ActualCost“: Zeigt die Gesamtnutzung und -kosten für den angegebenen Zeitraum an, die angefallen sind und auf Ihrer Rechnung ausgewiesen werden.
  - „AmortizedCost“: Zeigt die Gesamtnutzung und -kosten für den angegebenen Zeitraum unter Berücksichtigung der Amortisierung der relevanten Kosten für Reservierungskäufe an.
  - „Usage“: Alle Exporte, die vor dem 20. Juli 2020 erstellt wurden, sind vom Typ „Usage“. Aktualisieren Sie alle Ihre geplanten Exporte zu „ActualCost“ oder „AmortizedCost“.
- **Spalten**: Dient zum Definieren der Datenfelder, die in Ihre Exportdatei eingeschlossen werden sollen. Sie entsprechen den Feldern, die in der Nutzungsdetails-API verfügbar sind. Weitere Informationen finden Sie [hier](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Erstellen eines täglichen Exports für den bisherigen Kalendermonat für ein Abonnement

Anforderungs-URL: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Kopieren von großen Azure Storage-Blobs

Sie können mit Cost Management den Export der Azure-Nutzungsdaten in die Azure Storage-Konten als Blobs planen. Die resultierenden Blobgrößen können Hunderte von Gigabytes betragen. Das Cost Management-Team hat gemeinsam mit dem Azure Storage-Team das Kopieren umfangreicher Azure Storage-Blobs getestet. Die Ergebnisse sind in den folgenden Abschnitten dokumentiert. Beim Kopieren von Storage-Blobs zwischen Azure-Regionen sind ähnliche Ergebnisse zu erwarten.

Zum Testen der Leistung hat das Team Blobs aus Speicherkonten in der Region „USA, Westen“ in dieselbe Region und in andere Regionen übertragen. Das Team hat Geschwindigkeiten gemessen, die zwischen 2 GB pro Sekunde in derselben Region und 150 MB pro Sekunde bei der Übertragung in Speicherkonten in der Region „Asien, Südosten“ betrugen.

### <a name="test-configuration"></a>Testkonfiguration

Zum Messen der Geschwindigkeit von Blobübertragungen erstellte das Team eine einfache .NET-Konsolenanwendung, die über NuGet auf die neueste Version (2.0.1) der Azure Storage-Datenverschiebungsbibliothek (Data Movement Library, DLM) verweist. Die DLM ist ein vom Azure Storage Team bereitgestelltes SDK, das den programmgesteuerten Zugriff auf die Übertragungsdienste vereinfacht. Anschließend erstellte es V2-Standardspeicherkonten in mehreren Regionen und verwendeten „USA, Westen“ als Quellregion. Es füllte die dortigen Speicherkonten mit Containern auf, die jeweils zehn 2-GB-Blockblobs enthielten. Es kopierte die Container mithilfe der _CopyMethod.ServiceSideSyncCopy_-Option der _TransferManager.CopyDirectoryAsync()_ -Methode der DLM in andere Speicherkonten. Auf einem Computer mit Windows 10 und 12 Kernen und einem 1-GbE-Netzwerk wurden Tests ausgeführt.

Verwendete Anwendungseinstellungen:

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_. Das Team stellte fest, dass die Einstellung die größte Auswirkung auf den Gesamtdurchsatz hatte. Die Multiplikation der Anzahl der Kerne mit der Zahl 32 ergab den besten Durchsatz für den Testclient.
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_. Durch das Festlegen der Option auf den Höchstwert wird die vollständige Steuerung der Übertragungsparallelität an die obige Einstellung _ParallelOperations_ übergeben.
- _TransferManager.Configurations.BlockSize = 4,194,304_. Dies hatte Auswirkungen auf Übertragungsraten mit 4 MB und erwies sich als optimale Einstellung für die Tests.

Weitere Informationen und Beispielcode erhalten Sie über die Links im Abschnitt [Nächste Schritte](#next-steps).

### <a name="test-results"></a>Testergebnisse

| **Testnummer** | **Zielregion** | **Blobs** | **Zeit (in Sekunden)** | **MB/s** | **Kommentare** |
| --- | --- | --- | --- | --- | --- |
| 1 | USA, Westen | 2 GB x 10 | 10 | 2\.000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 4 parallele Übertragungen mit 8 Speicherkonten: 4 Westen zu 4 Osten pro Übertragung (Mittelwert) |
| 6 | EastUS | 2 GB × 10 x 4 | 92 | 870 | 4 parallele Übertragungen von einem Speicherkonto zu einem anderen |
| 5 | EastUS | 2 GB × 10 x 8 | 148 | 135 | 8 parallele Übertragungen mit 8 Speicherkonten: 4 Westen zu 4 x 2 Osten pro Übertragung (Mittelwert) |
| 7 | Südostasien | 2 GB x 10 | 133 | 150 |   |
| 8 | Südostasien | 2 GB x 10 x 4 | 444 | 180 | 4 parallele Übertragungen von einem Speicherkonto zu einem anderen |

### <a name="sync-transfer-characteristics"></a>Eigenschaften der synchronen Übertragung

Dies sind einige relevante Eigenschaften der mit der DML verwendeten Übertragung mit dienstseitiger Synchronisierung:

- Die DML kann ein einzelnes Blob oder ein Verzeichnis übertragen. Für die Verzeichnisübertragung können Sie für den Vergleich mit dem Blobpräfix ein Suchmuster verwenden.
- Blockblobübertragungen werden parallel ausgeführt. Geben Ende des Übertragungsvorgangs werden alle Übertragungen abgeschlossen. Einzelne Blobblöcke werden parallel übertragen.
- Die Übertragung wird asynchron auf dem Client ausgeführt. Der Übertragungsstatus ist in regelmäßigen Abständen über den Rückruf einer Methode verfügbar, die in einem _TransferContext_-Objekt definiert werden kann.
- Bei der Übertragung werden während des Fortschritts Prüfpunkte erstellt und ein _TransferCheckpoint_-Objekt verfügbar gemacht. Das Objekt stellt den letzten Prüfpunkt über das _TransferContext_-Objekt dar. Wenn der _TransferCheckpoint_ vor dem Abbruch einer Übertragung gespeichert wird, kann die Übertragung vom Prüfpunkt bis zu 7 Tage lang fortgesetzt werden. Die Übertragung kann an jedem Prüfpunkt fortgesetzt werden, nicht nur am letzten.
- Wenn der Übertragungsclientvorgang beendet und neu gestartet wird, erfolgt dies ohne Implementierung der Prüfpunktfunktion.
  - Bevor Blobübertragungen abgeschlossen wurden, wird die Übertragung neu gestartet.
  - Nachdem die Übertragung einiger Blobs abgeschlossen wurde, wird die Übertragung nur für die Blobs neu gestartet, deren Übertragung nicht abgeschlossen wurde.
- Durch das Anhalten der Clientausführung werden die Übertragungen angehalten.
- Die Blobübertragungsfunktion abstrahiert den Client von vorübergehenden Fehlern. Beispielsweise bewirkt eine Drosselung des Speicherkontos normalerweise nicht das Fehlschlagen der Übertragung, diese wird jedoch verlangsamt.
- Dienstseitige Übertragungen weisen eine geringe Auslastung der Clientressourcen für CPU und Arbeitsspeicher, Netzwerkbandbreite und Verbindungen auf.

### <a name="async-transfer-characteristics"></a>Eigenschaften der asynchronen Übertragung

Sie können die _TransferManager.CopyDirectoryAsync()_ -Methode mit der _CopyMethod.ServiceSideAsyncCopy_-Option aufrufen. Sie wird auf ähnliche Weise wie die synchrone Übertragung aus Clientperspektive ausgeführt, jedoch mit den folgenden Unterschieden:

- Die Übertragungsraten sind deutlich geringer als bei der entsprechenden synchronen Übertragung (in der Regel 10 MB/s oder weniger).
- Die Übertragung wird fortgesetzt, auch wenn der Clientprozess beendet wird.
- Obwohl Prüfpunkte unterstützt werden, erfolgt das Fortsetzen einer Übertragung mithilfe eines _TransferCheckpoint_ nicht auf Grundlage der Prüfpunktzeit, sondern auf Grundlage des aktuellen Status der Übertragung.

### <a name="test-summary"></a>Testzusammenfassung

Azure Blob Storage unterstützt mit der dienstseitigen synchronen Übertragungsfunktion hohe globale Übertragungsraten. Mit der Datenverschiebungsbibliothek ist die Verwendung der Funktion in .NET-Anwendungen unkompliziert. Bei Cost Management-Exporten können in weniger als einer Stunde mehrere hundert Gigabyte Daten in ein Speicherkonto kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

- Siehe den Quellcode in [Übertragen von Daten mit der Microsoft Azure Storage Data Movement-Bibliothek](https://github.com/Azure/azure-storage-net-data-movement).
- [Übertragen von Daten mit der Datenverschiebungsbibliothek](../../storage/common/storage-use-data-movement-library.md)
- Siehe das Quellcodebeispiel für die [Beispielanwendung AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup).
- Lesen Sie [High-Throughput with Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage) (Hoher Durchsatz mit Azure Blob Storage, in englischer Sprache).