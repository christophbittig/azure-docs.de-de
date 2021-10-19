---
title: Azure Storage-Blobbestand
description: Der Azure Storage-Bestand ist ein Tool, mit dem Sie eine Übersicht über alle Ihre Blobdaten in einem Speicherkonto erhalten.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 6962688a574d7f7c11f8cbfc71ccdb29ac3b6445
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619386"
---
# <a name="azure-storage-blob-inventory"></a>Azure Storage-Blobbestand

Das Blobinventur-Feature von Azure Storage bietet eine Übersicht über Ihre Container, Blobs, Momentaufnahmen und Blobversionen innerhalb eines Speicherkontos. Nutzen Sie den Inventurbericht, um verschiedene Attribute von Blobs und Containern zu verstehen, z. B. die Gesamtdatengröße, das Alter, den Verschlüsselungsstatus, die Unveränderlichkeitsrichtlinie, die gesetzliche Aufbewahrungspflicht und so weiter. Der Bericht enthält eine Übersicht über Ihre Daten, die Sie auch für Geschäfts- und Complianceanforderungen nutzen können.

## <a name="inventory-features"></a>Inventurfeatures

Die folgende Liste beschreibt Features und Funktionen, die im aktuellen Release der Azure Storage-Blobinventur verfügbar sind.

- **Inventurberichte für Blobs und Container**

  Sie können Inventurberichte für Blobs und Container erstellen. Ein Blobbericht kann Basisblobs, Momentaufnahmen, Blobversionen und die damit verbunden Eigenschaften (z. B. den Zeitpunkt der Erstellung oder den Zeitpunkt der letzten Änderung) enthalten. Ein Containerbericht beschreibt Container und die damit verbundenen Eigenschaften (z. B. den Status der Unveränderlichkeitsrichtlinie oder den Status der gesetzlichen Aufbewahrungspflicht).

- **Benutzerdefiniertes Schema**

  Sie können auswählen, welche Felder im Bericht enthalten sind. Wählen Sie diese aus einer Liste unterstützter Felder aus. Die Liste finden Sie weiter unten in diesem Artikel.

- **CSV- und Apache Parquet-Ausgabeformat**

  Sie können den Inventurbericht entweder in einem CSV- oder einem Apache Parquet-Ausgabeformat erstellen.

- **Manifestdatei und Azure Event Grid-Ereignis pro Inventurbericht**

  Pro Inventurbericht werden eine Manifestdatei und ein Azure Event Grid-Ereignis erstellt. Diese werden weiter unten in diesem Artikel beschrieben.

## <a name="enabling-inventory-reports"></a>Aktivieren von Inventurberichten

Sie aktivieren Blobinventurberichte, indem Sie Ihrem Speicherkonto eine Richtlinie mit mindestens einer Regel hinzufügen. Weitere Anweisungen dazu finden Sie unter [Aktivieren von Azure Storage-Blobinventurberichten](blob-inventory-how-to.md).

## <a name="upgrading-an-inventory-policy"></a>Aktualisieren einer Inventurrichtlinie

Wenn Sie die Azure Storage-Blobinventur bereits nutzen und sie vor Juni 2021 konfiguriert haben, müssen Sie für die Nutzung der neuen Features nur die Richtlinie laden und sie wieder abspeichern, nachdem Sie die Änderungen vorgenommen haben. Wenn Sie die Richtlinie dann erneut laden, werden die neuen Felder in der Richtlinie mit den Standardwerten ausgefüllt. Sie können diese Werte bei Bedarf ändern. Darüber hinaus sind die beiden folgenden Features verfügbar.

- Ein Zielcontainer wird nun für jede Regel und nicht mehr nur für die Richtlinie unterstützt.

- Nun werden pro Regel (und nicht mehr nur pro Richtlinie) eine Manifestdatei und ein Azure Event Grid-Ereignis erstellt.

## <a name="inventory-policy"></a>Inventurrichtlinie

Eine Inventurrichtlinie ist eine Sammlung von Regeln in einem JSON-Dokument.

```json
{
  "enabled": true,
  "rules": [
  {
    "enabled": true,
    "name": "inventoryrule1",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  },
  {
    "enabled": true,
    "name": "inventoryrule2",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  }]
}
```

Sie zeigen den JSON-Code für eine Inventurrichtlinie an, indem Sie im Azure-Portal im Abschnitt **Blob Inventory** (Blobbestand) die Registerkarte **Codeansicht** auswählen.

| Parametername | Parametertyp | Notizen | Erforderlich? |
|--|--|--|--|
| enabled | boolean | Dient zum Deaktivieren der gesamten Richtlinie. Wenn diese Option auf **true** festgelegt ist, überschreibt das aktivierte Feld auf Regelebene diesen Parameter. Wenn diese Option deaktiviert ist, wird die Inventur für alle Regeln deaktiviert. | Ja |
| rules | Ein Array von Regelobjekten | Eine Richtlinie muss mindestens eine Regel enthalten. Pro Richtlinie werden bis zu 100 Regeln unterstützt. | Ja |

## <a name="inventory-rules"></a>Inventurregeln

Eine Regel erfasst die Filterbedingungen und Ausgabeparameter zum Erstellen eines Bestandsberichts. Jeder Regel erstellt einen Bestandsbericht. Regeln können überlappende Präfixe aufweisen. Ein Blob kann abhängig von den Regeldefinitionen auch in mehreren Beständen enthalten sein.

Jede Regel in der Richtlinie umfasst mehrere Parameter:

| Parametername | Parametertyp | Notizen | Erforderlich? |
|--|--|--|--|
| name | Zeichenfolge | Ein Regelname kann bis zu 256 alphanumerische Zeichen (Groß-/Kleinschreibung beachten) enthalten. Der Name muss innerhalb einer Richtlinie eindeutig sein. | Ja |
| enabled | boolean | Ein Flag, das es ermöglicht, eine Regel zu aktivieren oder zu deaktivieren. Der Standardwert lautet **true**. | Ja |
| Definition | JSON-Definition der Inventurregel | Jede Definition beinhaltet einen Regelfiltersatz. | Ja |
| destination | Zeichenfolge | Der Zielcontainer, in dem alle Bestandsdateien generiert werden. Der Zielcontainer muss bereits vorhanden sein. |

Das globale Flag **Blob inventory enabled** (Blobinventur aktiviert) hat Vorrang vor dem *enabled*-Parameter in einer Regel.

### <a name="rule-definition"></a>Regeldefinition

| Parametername | Parametertyp | Notizen | Erforderlich |
|--|--|--|--|
| Filter | json | Mit Filtern wird entschieden, ob ein Blob oder ein Container Teil einer Inventur ist oder nicht. | Ja |
| format | Zeichenfolge | Bestimmt die Ausgabe der Inventurdatei. Gültige Werte sind `csv` (für das CSV-Format) und `parquet` (für das Apache Parquet-Format).| Ja |
| objectType | Zeichenfolge | Gibt an, ob es sich um eine Inventurregel für Blobs oder für Container handelt. Gültige Werte sind `blob` und `container`. |Ja |
| schedule | Zeichenfolge | Zeitplan für die Ausführung dieser Regel. Gültige Werte sind `daily` und `weekly`. | Ja |
| schemaFields | JSON-Array | Liste von Schemafeldern, die Teil der Inventur sind. | Ja |

### <a name="rule-filters"></a>Regelfilter

Zum Anpassen eines Blobbestandsberichts sind mehrere Filter verfügbar:

| Filtername | Filtertyp | Notizen | Erforderlich? |
|--|--|--|--|
| blobTypes | Ein Array von vordefinierten Enumerationswerten. | Gültige Werte sind `blockBlob` und `appendBlob` für Konten mit aktiviertem hierarchischem Namespace sowie `blockBlob`, `appendBlob` und `pageBlob` für andere Konten. Dieses Feld ist bei einer Containerinventur nicht anwendbar, (objectType: `container`). | Ja |
| prefixMatch | Ein Array von bis zu 10 Zeichenfolgen für Präfixe, die abgeglichen werden sollen. | Wenn Sie *prefixMatch* nicht definieren oder ein leeres Präfix angeben, gilt die Regel für alle Blobs im Speicherkonto. Bei dem Präfix muss es sich um das Präfix eines Containernamens oder um einen Containernamen handeln. Platzhalter in einer derartigen Schreibweise sind z.B. `container` und `container1/foo`. | Nein |
| includeSnapshots | boolean | Gibt an, ob Momentaufnahmen in die Inventur eingeschlossen werden sollen. Der Standardwert ist `false`. Dieses Feld ist bei einer Containerinventur nicht anwendbar, (objectType: `container`). | Nein |
| includeBlobVersions | boolean | Gibt an, ob Blobversionen in die Inventur eingeschlossen werden sollen. Der Standardwert ist `false`. Dieses Feld ist bei einer Containerinventur nicht anwendbar, (objectType: `container`). | Nein |

Sie zeigen den JSON-Code für Inventurrichtlinien an, indem Sie im Azure-Portal im Abschnitt **Blob Inventory** (Blobbestand) die Registerkarte **Codeansicht** auswählen. Filter werden innerhalb einer Regeldefinition angegeben.

```json
{
  "destination": "inventory-destination-container",
  "enabled": true,
  "rules": [
  {
    "definition": {
      "filters": {
        "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"],
        "includeSnapshots": false,
        "includeBlobVersions": true,
      },
      "format": "csv",
      "objectType": "blob",
      "schedule": "daily",
      "schemaFields": ["Name", "Creation-Time"]
    },
    "enabled": true,
    "name": "blobinventorytest",
    "destination": "inventorydestinationContainer"
  },
  {
    "definition": {
      "filters": {
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"]
      },
      "format": "csv",
      "objectType": "container",
      "schedule": "weekly",
      "schemaFields": ["Name", "HasImmutabilityPolicy", "HasLegalHold"]
    },
    "enabled": true,
    "name": "containerinventorytest",
    "destination": "inventorydestinationContainer"
    }
  ]
}
```

### <a name="custom-schema-fields-supported-for-blob-inventory"></a>Benutzerdefinierte Schemafelder, die für die Blobinventur unterstützt werden

- Name (Erforderlich)
- Erstellungszeit
- Last-Modified
- Content-Length
- Content-MD5
- BlobType
- AccessTier
- AccessTierChangeTime
- Expiry-Time
- hdi_isfolder
- Besitzer
- Group
- Berechtigungen
- Acl
- Momentaufnahme (verfügbar und erforderlich, wenn Sie Momentaufnahmen in Ihren Bericht integrieren möchten)
- VersionID (verfügbar und erforderlich, wenn Sie Blobversionen in Ihren Bericht integrieren möchten)
- IsCurrentVersion (verfügbar und erforderlich, wenn Sie Blobversionen in Ihren Bericht integrieren möchten)
- Metadaten
- LastAccessTime

### <a name="custom-schema-fields-supported-for-container-inventory"></a>Benutzerdefinierte Schemafelder, die für die Containerinventur unterstützt werden

- Name (Erforderlich)
- Last-Modified
- LeaseStatus
- LeaseState
- LeaseDuration
- PublicAccess
- HasImmutabilityPolicy
- HasLegalHold
- Metadaten

## <a name="inventory-run"></a>Inventurausführung

Es wird automatisch eine tägliche Blobinventur geplant. Die Inventur kann bis zum Abschluss bis zu 24 Stunden benötigen. Sie konfigurieren einen Bestandsbericht, indem Sie eine Inventurrichtlinie mit mindestens einer Regel hinzufügen.

Bestandsrichtlinien werden vollständig gelesen oder geschrieben. Teilaktualisierungen werden nicht unterstützt.

> [!IMPORTANT]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden Bestandsanforderungen unter Umständen blockiert. Sie können die Sperre dieser Anforderungen durch Bereitstellen von Ausnahmen für vertrauenswürdige Microsoft-Dienste aufheben. Weitere Informationen finden Sie im Abschnitt „Ausnahmen“ unter [Konfigurieren von Firewalls und virtuellen Netzwerken](../common/storage-network-security.md#exceptions).

## <a name="inventory-completed-event"></a>Inventurabschlussereignis

Das `BlobInventoryPolicyCompleted`-Ereignis wird erstellt, sobald die Inventur für eine Regel abgeschlossen wurde. Dieses Ereignis tritt auch ein, wenn vor dem Start der Inventur ein Benutzerfehler auftritt. Beispielsweise wird dieses Ereignis durch eine ungültige Richtlinie oder durch einen Fehler aufgrund eines fehlenden Zielcontainers ausgelöst. Die folgende JSON zeigt ein `BlobInventoryPolicyCompleted`-Ereignis als Beispiel:

```json
{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "data": {
    "scheduleDateTime": "2021-05-28T03:50:27Z",
    "accountName": "testaccount",
    "ruleName": "Rule_1",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "manifestBlobUrl": "https://testaccount.blob.core.windows.net/inventory-destination-container/2021/05/26/13-25-36/Rule_1/Rule_1.csv"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-05-28T15:03:18Z"
}
```

In folgender Tabelle wird das Schema des `BlobInventoryPolicyCompleted`-Ereignisses beschrieben.

|Feld|type|BESCHREIBUNG|
|---|---|
|scheduleDateTime|Zeichenfolge|Zeitpunkt, an dem die Inventurrichtlinie geplant ist.|
|.<Name der Region|Zeichenfolge|Der Name des Speicherkontos.|
|ruleName|Zeichenfolge|Name der Regel|
|policyRunStatus|Zeichenfolge|Status der Inventurausführung Mögliche Werte sind `Succeeded`, `PartiallySucceeded` und `Failed`.|
|policyRunStatusMessage|Zeichenfolge|Statusmeldung der Inventurausführung|
|policyRunId|Zeichenfolge|Richtlinienausführungs-ID für die Inventurausführung|
|manifestBlobUrl|Zeichenfolge|Blob-URL für die Manifestdatei der Inventurausführung|

## <a name="inventory-output"></a>Inventurausgabe

Jede Inventurregel generiert Dateien im angegebenen Inventur-Zielcontainer für die jeweilige Regel. Die Inventurausgabe wird unter folgendem Pfad generiert: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/<ruleName`. Dabei gilt:

- *accountName* ist der Name Ihres Azure Blob Storage-Kontos.
- *inventory-destination-container* ist der in Ihrer Inventurregel angegebene Zielcontainer.
- *YYYY/MM/DD/HH-MM-SS* ist die Uhrzeit, zu der die Inventur gestartet wurde.
- *ruleName* ist der Name der Inventurregel.

### <a name="inventory-files"></a>Inventurdateien

Bei jeder Inventur für eine Regel werden die folgenden Dateien generiert:

- **Inventurdatei:** Eine Inventurausführung für eine Regel generiert mindestens eine Datei im CSV- oder Apache Parquet-Format. Wenn die Anzahl der übereinstimmenden Objekte sehr hoch ist, werden mehrere Dateien erstellt. Jede dieser Dateien enthält die übereinstimmenden Objekte und ihre Metadaten. Die erste Zeile in einer CSV-Datei enthält immer das Schema. Die folgende Abbildung zeigt eine in Microsoft Excel geöffnete CSV-Bestandsdatei.

  :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Screenshot einer in Microsoft Excel geöffneten CSV-Bestandsdatei":::

  > [!NOTE]
  > In Berichten Apache Parquet-Format werden Daten im folgenden Format dargestellt: `timestamp_millis [number of milliseconds since 1970-01-01 00:00:00 UTC`.

- **Prüfsummendatei:** Eine Prüfsummendatei enthält die MD5-Prüfsumme des Inhalts der Datei „manifest.json“. Der Name der Prüfsummendatei lautet `<ruleName>-manifest.checksum`. Die Generierung der Prüfsummendatei markiert das Ende der Inventur für eine Regel.

- **Manifestdatei:** Eine Datei „manifest.json“ enthält Details zu den Inventurdateien, die für die jeweilige Regel generiert wurden. Der Name der Datei lautet `<ruleName>-manifest.json`. Diese Datei enthält auch die vom Benutzer angegebene Regeldefinition und den Pfad zum Bestand zu dieser Regel. Die folgende JSON zeigt den Inhalt einer manifest.json-Datei als Beispiel.

  ```json
  {
  "destinationContainer" : "inventory-destination-container",
  "endpoint" : "https://testaccount.blob.core.windows.net",
  "files" : [
    {
      "blob" : "2021/05/26/13-25-36/Rule_1/Rule_1.csv",
      "size" : 12710092
    }
  ],
  "inventoryCompletionTime" : "2021-05-26T13:35:56Z",
  "inventoryStartTime" : "2021-05-26T13:25:36Z",
  "ruleDefinition" : {
    "filters" : {
      "blobTypes" : [ "blockBlob" ],
      "includeBlobVersions" : false,
      "includeSnapshots" : false,
      "prefixMatch" : [ "penner-test-container-100003" ]
    },
    "format" : "csv",
    "objectType" : "blob",
    "schedule" : "daily",
    "schemaFields" : [
      "Name",
      "Creation-Time",
      "BlobType",
      "Content-Length",
      "LastAccessTime",
      "Last-Modified",
      "Metadata",
      "AccessTier"
    ]
  },
  "ruleName" : "Rule_1",
  "status" : "Succeeded",
  "summary" : {
    "objectCount" : 110000,
    "totalObjectSize" : 23789775
  },
  "version" : "1.0"
  }
  ```

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Die Preise für die Inventur basieren auf der Anzahl der Blobs und Container, die während des Abrechnungszeitraums gescannt werden. Beispiel: Ein Konto verfügt über eine Millionen Blobs und die Blobinventur wird einmal pro Woche durchgeführt. Nach vier Wochen wurden dann vier Millionen Blobeinträge gescannt.

Der Abrechnungszeitraum für die Blobinventur beginnt am 1. Oktober 2021. Zu diesem Zeitpunkt werden auch die regionalen Preise veröffentlicht. Der Basispreis ohne regionale Anpassung liegt bei ca. $0,0025 USD pro 1 Million gescannter Blobspeichereinträge und bei ca. $0,0035 USD, wenn Data Lake Storage Gen2 aktiviert ist. Nachdem die Inventurdateien erstellt wurden, fallen standardmäßig zusätzliche Datenspeicherungs- und Vorgangskosten im Zusammenhang mit dem Speichern, Lesen und Schreiben der durch die Inventur erstellten Dateien in dem Konto an.

Nachdem ein Inventurbericht vervollständigt wurde, fallen standardmäßig zusätzliche Datenspeicherungs- und Vorgangskosten im Zusammenhang mit dem Speichern, Lesen und Schreiben des Inventurberichts im Speicherkonto an.

Wenn eine Regel ein Präfix enthält, das mit dem Präfix einer anderen Regel überlappt, dann kann derselbe Blob in mehreren Inventurberichten auftauchen. In diesem Fall werden beide Instanzen abgerechnet. Gehen wir z. B. davon aus, dass das Element `prefixMatch` einer Regel auf `["inventory-blob-1", "inventory-blob-2"]` festgelegt ist, und dass das Element `prefixMatch` einer anderen Regel auf `["inventory-blob-10", "inventory-blob-20"]` festgelegt ist. Ein Objekt mit dem Namen `inventory-blob-200` wird in beiden Inventurberichten erscheinen.

Momentaufnahmen und Blobversionen werden ebenfalls abgerechnet, auch wenn Sie die Filter `includeSnapshots` und `includeVersions` auf `false` festgelegt haben. Diese Filterwerte haben keinen Einfluss auf die Abrechnung. Sie können sie nur verwenden, um zu filtern, welche Elemente im Bericht erscheinen.

Weitere Informationen zu den Preisen für die Azure Storage-Blobinventur finden Sie auf der Seite [Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp | Blob Storage (Standardunterstützung) | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup>              | ![Ja](../media/icons/yes-icon.png) <sup>2</sup> |
| Premium-Blockblobs | ![Ja](../media/icons/yes-icon.png)| ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) erfordern beide ein Speicherkonto mit einem aktivierten hierarchischen Namespace.

<sup>2</sup> Die Funktion wird auf der Vorschauebene unterstützt.

## <a name="known-issues"></a>Bekannte Probleme

In diesem Abschnitt werden Einschränkungen und bekannte Probleme des Azure Storage-Blobbestandsfeatures beschrieben.

### <a name="inventory-job-fails-to-complete-for-hierarchical-namespace-enabled-accounts"></a>Inventurauftrag kann für Konten mit aktiviertem hierarchischen Namespace nicht abgeschlossen werden

Für ein Konto mit hunderten Millionen von Blobs und aktiviertem hierarchischen Namespace kann der Inventurauftrag möglicherweise nicht innerhalb von 24 Stunden abgeschlossen werden. In diesem Fall wird keine Bestandsdatei erstellt.

### <a name="inventory-job-cannot-write-inventory-reports"></a>Inventurauftrag kann keine Inventurberichte schreiben

Der Inventurauftrag wird möglicherweise durch eine Objektreplikationsrichtlinie daran gehindert, Inventurberichte in den Zielcontainer zu schreiben. Es gibt noch ein paar andere Szenarien, in denen Berichte archiviert werden oder in denen sie nicht mehr geändert werden können, wenn sie teilweise abgeschlossen sind. Dadurch kann der Inventurauftrag fehlschlagen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von Azure Storage-Blobinventurberichten](blob-inventory-how-to.md)
- [Berechnen der Anzahl und Gesamtgröße von Blobs pro Container](calculate-blob-count-size.md)
- [Verwalten des Azure Blob Storage-Lebenszyklus](./lifecycle-management-overview.md)
