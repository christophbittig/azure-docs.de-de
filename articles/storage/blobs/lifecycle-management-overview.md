---
title: Optimieren von Kosten durch automatisches Verwalten des Datenlebenszyklus
titleSuffix: Azure Storage
description: Verwenden Sie Azure Storage Lebenszyklusverwaltungsrichtlinien, um automatisierte Regeln zum Verschieben von Daten zwischen den Ebenen „Heiß“, „Kalt“ und „Archiv“ zu erstellen.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 69cb9048ef723744da93478b01ba7122152e1db9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460303"
---
# <a name="optimize-costs-by-automatically-managing-the-data-lifecycle"></a>Optimieren von Kosten durch automatisches Verwalten des Datenlebenszyklus

Datasets haben eindeutige Lebenszyklen. Früh im Lebenszyklus greifen Benutzer häufig auf einige Daten zu. Aber häufig sinkt der Zugriffsbedarf mit zunehmendem Alter der Daten drastisch. Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur selten zugegriffen wird. Einige Daten laufen Tage oder Monate nach der Erstellung ab, während andere Daten im Verlauf ihrer gesamten Lebensdauer aktiv gelesen und geändert werden. Die Azure Storage-Lebenszyklusverwaltung bietet eine regelbasierte Richtlinie, mit deren Hilfe Sie den Übergang von Blob-Daten auf die optimalen Zugriffsebenen und den Ablauf der Daten am Ende des Datenlebenszyklus umsetzen können.

Mit der Richtlinie für die Lebenszyklusverwaltung können Sie die folgenden Aufgaben ausführen:

- Sofortiger Übergang von kalten zu heißen Blobs, wenn darauf zugegriffen wird, um die Leistung zu optimieren.
- Übergang von Blobs, Blobversionen und Blobmomentaufnahmen aus Kostengründen zu einer kälteren Speicherebene, wenn für einen bestimmten Zeitraum nicht darauf zugegriffen wurde bzw. sie nicht geändert wurden. In diesem Szenario kann die Lebenszyklusverwaltungsrichtlinie Objekte von „Heiß“ zu „Kalt“, von „Heiß“ in „Archiv“ oder von „Kalt“ in „Archiv“ verschieben.
- Löschen von Blobs, Blobversionen und Blobmomentaufnahmen am Ende ihrer Lebenszyklen.
- Definieren von Regeln, die einmal täglich auf Speicherkontoebene ausgeführt werden
- Anwendung von Regeln auf Container oder eine Teilmenge von Blobs (mit Namenspräfixen oder [Blob-Indextags](storage-manage-find-blobs.md) als Filter).

Stellen Sie sich ein Szenario vor, bei dem in den frühen Stages des Lebenszyklus häufig auf Daten zugegriffen wird, nach zwei Wochen aber nur noch gelegentlich. Nach dem ersten Monat wird auf das Dataset nur noch selten zugegriffen. In diesem Szenario empfiehlt sich in den frühen Phasen heißer Speicher. Die kalte Speicherebene eignet sich am besten für den gelegentlichen Zugriff. Die Archivspeicherebene ist die beste Option, wenn die Daten mehr als einen Monat alt sind. Indem Sie Daten basierend auf ihrem Alter mit Richtlinienregeln für die Lebenszyklusverwaltung auf die entsprechende Speicherebene verschieben, können Sie die kostengünstigste Lösung für Ihre Anforderungen entwerfen.

Lebenszyklusverwaltungsrichtlinien werden für Blockblobs und Anfügeblobs in Konten vom Typ „Allgemein v2“, „Premium-Blockblob“ und „Blob Storage“ unterstützt. Die Lebenszyklusverwaltung hat keinen Einfluss auf Systemcontainer wie die *$logs* oder *$web*-Container.

> [!IMPORTANT]
> Wenn ein DataSet lesbar sein muss, legen Sie keine Richtlinie zum Verschieben von Blobs auf die Archivebene fest. Blobs auf der Archivebene können nur gelesen werden, wenn sie zum ersten Mal aktiviert werden. Dies ist ein Prozess, der zeitaufwändig und teuer sein kann. Weitere Informationen finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).

## <a name="lifecycle-management-policy-definition"></a>Definition der Richtlinie zur Lebenszyklusverwaltung

Eine Richtlinie zur Lebenszyklusverwaltung ist eine Sammlung von Regeln in einem JSON-Dokument. Das folgende JSON-Beispiel zeigt eine vollständige Regeldefinition:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Eine Richtlinie ist eine Sammlung von Regeln, wie in der folgenden Tabelle beschrieben:

| Parametername | Parametertyp | Notizen |
|----------------|----------------|-------|
| `rules`        | Ein Array von Regelobjekten | Eine Richtlinie muss mindestens eine Regel enthalten. Sie können in einer Richtlinie bis zu 100 Regeln definieren.|

Jede Regel innerhalb der Richtlinie verfügt über mehrere Parameter, die in der folgenden Tabelle beschrieben werden:

| Parametername | Parametertyp | Notizen | Erforderlich |
|--|--|--|--|
| `name` | String | Ein Regelname kann bis zu 256 alphanumerische Zeichen enthalten. Bei Regelnamen wird die Groß-/Kleinschreibung unterschieden. Er muss innerhalb einer Richtlinie eindeutig sein. | True |
| `enabled` | Boolean | Ein optionaler boolescher Wert, über den eine Regel temporär deaktiviert werden kann. Der Standardwert ist „True“, wenn dieser Wert nicht festgelegt wurde. | False |
| `type` | Ein Enumerationswert | Aktuell ist `Lifecycle` der gültige Typ. | True |
| `definition` | Ein Objekt, das die Lebenszyklusregel definiert | Jede Definition beinhaltet einen Filtersatz und einen Aktionssatz. | True |

## <a name="lifecycle-management-rule-definition"></a>Definition der Lebenszyklusverwaltungsregel

Jede Regeldefinition innerhalb einer Richtlinie enthält einen Filtersatz und einen Aktionssatz. Der [Filtersatz](#rule-filters) schränkt Regelaktionen auf eine bestimmte Menge von Objekten innerhalb eines Containers oder auf Objektnamen ein. Der [Aktionssatz](#rule-actions) wendet die Ebenen- oder Löschaktionen auf den gefilterten Satz von Objekten an.

### <a name="sample-rule"></a>Beispielregel

Mit der folgenden Beispielregel wird das Konto so gefiltert, dass Aktionen für Objekte ausgeführt werden, die sich in `sample-container` befinden und mit `blob1` beginnen.

- Blob 30 Tage nach der letzten Änderung in die kalte Ebene verschieben
- Blob 90 Tage nach der letzten Änderung in die Archivebene verschieben
- Blob 2.555 Tage (sieben Jahre) nach der letzten Änderung löschen
- Löschen früherer Blobversionen 90 Tage nach der Erstellung

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "sample-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "sample-container/blob1"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Regelfilter

Filter schränken Regelaktionen auf eine Teilmenge der Blobs innerhalb des Speicherkontos ein. Wenn mehrere Filter definiert sind, wird ein logisches `AND` für alle Filter ausgeführt.

Filter umfassen Folgendes:

| Filtername | Filtertyp | Notizen | Ist erforderlich |
|-------------|-------------|-------|-------------|
| blobTypes   | Ein Array von vordefinierten Enumerationswerten. | In der aktuellen Version werden `blockBlob` und `appendBlob` unterstützt. Für `appendBlob` werden nur Blobs zum Löschen unterstützt, festgelegte Ebenen dagegen nicht. | Ja |
| prefixMatch | Ein Array von Zeichenfolgen für Präfixe, die abgeglichen werden sollen. Jede Regel kann bis zu zehn Präfixe definieren, bei denen die Groß- und Kleinschreibung beachtet wird. Eine Präfixzeichenfolge muss mit einem Containernamen beginnen. Wenn Sie für eine Regel beispielsweise eine Übereinstimmung mit allen Blobs unter „`https://myaccount.blob.core.windows.net/sample-container/blob1/...`“ erzielen möchten, lautet der prefixMatch-Wert `sample-container/blob1`. | Wenn Sie prefixMatch nicht definieren, gilt die Regel für alle Blobs im Speicherkonto. | Nein |
| blobIndexMatch | Ein Array von Wörterbuchwerten, die aus dem Blobindextag-Schlüssel und den Wertbedingungen bestehen, die abgeglichen werden sollen. In jeder Regel können bis zu 10 Blobindextag-Bedingungen definiert werden. Wenn Sie beispielsweise alle Blobs mit `Project = Contoso` unter `https://myaccount.blob.core.windows.net/` für eine Regel abgleichen möchten, lautet der blobIndexMatch-Wert `{"name": "Project","op": "==","value": "Contoso"}`. | Wenn Sie blobIndexMatch nicht definieren, gilt die Regel für alle Blobs im Speicherkonto. | Nein |

Weitere Informationen zum Blob-Index-Feature sowie zu bekannten Problemen und Einschränkungen finden Sie unter [Verwalten und Suchen von Daten in Azure Blob Storage mit Blob-Index](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Regelaktionen

Aktionen werden auf die gefilterten Blobs angewandt, wenn die Ausführungsbedingung erfüllt ist.

Die Lebenszyklusverwaltung unterstützt Ebenenverschiebung und Löschen von Blobs, früheren Blobversionen und Blobmomentaufnahmen. Definieren Sie mindestens eine Aktion für jede Regel für Basisblobs, früheren Blobversionen oder Blobmomentaufnahmen.

| Aktion                      | Basisblob                                  | Momentaufnahme      | Version
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Unterstützt für `blockBlob`                  | Unterstützt     | Unterstützt     |
| enableAutoTierToHotFromCool | Unterstützt für `blockBlob`                  | Nicht unterstützt | Nicht unterstützt |
| tierToArchive               | Unterstützt für `blockBlob`                  | Unterstützt     | Unterstützt     |
| delete                      | Für `blockBlob` und `appendBlob` unterstützt | Unterstützt     | Unterstützt     |

> [!NOTE]
> Wenn für das gleiche Blob mehrere Aktionen definiert sind, wendet die Lebenszyklusverwaltung die am wenigsten teure Aktion auf das Blob an. Beispielsweise ist die Aktion `delete` kostengünstiger als die Aktion `tierToArchive`. Die Aktion `tierToArchive` ist kostengünstiger als die Option `tierToCool`.

Die Ausführungsbedingungen basieren auf dem Alter. Basisblobs verwenden den Zeitpunkt der letzten Änderung, Blobversionen den Erstellungszeitpunkt und Blobmomentaufnahmen den Erstellungszeitpunkt der Momentaufnahme, um das Alter nachzuverfolgen.

| Aktionsausführungsbedingung | Wert der Bedingung | BESCHREIBUNG |
|--|--|--|
| daysAfterModificationGreaterThan | Ganzzahliger Wert, der das Alter in Tagen angibt | Bedingung für Basisblobaktionen |
| daysAfterCreationGreaterThan | Ganzzahliger Wert, der das Alter in Tagen angibt | Bedingung für Aktionen für Blobversionen und Blobmomentaufnahmen |
| daysAfterLastAccessTimeGreaterThan | Ganzzahliger Wert, der das Alter in Tagen angibt | Die Bedingung für Basis-Blob-Aktionen, wenn die Zugriffsverfolgung aktiviert ist. |

## <a name="examples-of-lifecycle-policies"></a>Beispiele für Richtlinien für den Lebenszyklus

Die folgenden Beispiele veranschaulichen die Behandlung von gängigen Szenarien mithilfe von Regeln zur Lebenszyklusverwaltung.

### <a name="move-aging-data-to-a-cooler-tier"></a>Verschieben von alternden Daten auf eine kühlere Ebene

Dieses Beispiel zeigt den Übergang von Blockblobs mit dem Präfix `sample-container/blob1` oder `container2/blob2`. Die Richtlinie überführt Blobs, die mehr als 30 Tage nicht verändert wurden, in den kalten Speicher und Blobs, die in 90 Tagen nicht verändert wurden, in die Archivebene:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "sample-container/blob1", "container2/blob2" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-time"></a>Verschieben von Daten basierend auf dem Datum des letzten Zugriffs

Sie können die Nachverfolgung des letzten Zugriffs aktivieren, um zu erfassen, wann Ihr Blob zuletzt gelesen oder geschrieben wurde, und als Filter zum Verwalten des Tierings und der Aufbewahrung Ihrer Blobdaten. Informationen zum Aktivieren der Nachverfolgung des letzten Zugriffs finden Sie unter [Optionales Aktivieren der Nachverfolgung der Zugriffszeit](lifecycle-management-policy-configure.md#optionally-enable-access-time-tracking).

Wenn die Zeitüberwachung für den letzten Zugriff aktiviert ist, wird die Blobeigenschaft, mit der `LastAccessTime` aufgerufen wird, aktualisiert, wenn ein Blob gelesen oder beschrieben wird. Dabei gilt der Vorgang [Get Blob](/rest/api/storageservices/get-blob) als Zugriff. [Get Blob Properties](/rest/api/storageservices/get-blob-properties), [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata) und [Get Blob Tags](/rest/api/storageservices/get-blob-tags) gelten nicht als Zugriff. Daher wird bei diesen Vorgängen die Uhrzeit des letzten Zugriffs nicht aktualisiert.

Um die Auswirkungen auf die Lesezugriffswartezeit zu minimieren, wird die Uhrzeit des letzten Zugriffs nur beim ersten Lesezugriff der letzten 24 Stunden aktualisiert. Bei nachfolgenden Lesezugriffen innerhalb desselben 24-Stunden-Zeitraums wird die Uhrzeit des letzten Zugriffs nicht aktualisiert. Wenn ein Blob zwischen zwei Lesezugriffen geändert wird, ist die Uhrzeit des letzten Zugriffs die aktuellere der beiden Werte.

Im nachstehenden Beispiel werden Blobs in den kalten Speicher verschoben, wenn auf sie während 30 Tagen nicht zugegriffen wurde. Bei der `enableAutoTierToHotFromCool`-Eigenschaft handelt es sich um einen booleschen Wert, der angibt, ob für einen Blob automatisch ein Tiering von der kalten Ebene zurück zur heißen Ebene durchgeführt werden soll, wenn nach dem Tiering auf die kalte Ebene noch mal auf den Blob zugegriffen wird.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

### <a name="archive-data-after-ingest"></a>Archivieren von Daten nach der Erfassung

Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nur sehr selten oder gar nicht zugegriffen wird. Die folgende Lebenszyklusrichtlinie ist so konfiguriert, dass Daten kurz nach der Erfassung archiviert werden. In diesem Beispiel werden Blockblobs im Container `archivecontainer` an eine Archivebene überführt. Die Umstellung wird durch die Ausführung der Aktion für Blobs 0 Tage nach dem Zeitpunkt der letzten Änderung erreicht:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

> [!NOTE]
> Microsoft empfiehlt, Ihre Blobs aus Effizienzgründen direkt auf die Archivebene hochzuladen. Sie können die Archivebene im Header *x-ms-access-tier* für den Vorgang [Put Blob](/rest/api/storageservices/put-blob) (Blob festlegen) oder [Put Block List](/rest/api/storageservices/put-block-list) (Blockliste festlegen) angeben. Der Header *x-ms-access-tier* wird von der REST-Version 2018-11-09 und höher oder den neuesten Blobspeicher-Clientbibliotheken unterstützt.

### <a name="expire-data-based-on-age"></a>Ablauf von Daten nach dem Alter

Für einige Daten wird erwartet, dass sie einige Tage oder Monate nach der Erstellung ablaufen. Sie können eine Richtlinie zur Lebenszyklusverwaltung so einrichten, dass Daten durch Löschung auf der Grundlage ihres Alters ablaufen. Das folgende Beispiel zeigt eine Richtlinie, mit der alle Blockblobs gelöscht werden, die älter als 365 Tage sind.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Löschen von Daten mit Blobindextags

Einige Daten sollten nur ablaufen, wenn sie explizit zur Löschung gekennzeichnet sind. Sie können eine Lebenszyklusverwaltungsrichtlinie so konfigurieren, dass Daten ablaufen, die mit Schlüssel-Wert-Attributen für den Blobindex gekennzeichnet sind. Im folgenden Beispiel ist eine Richtlinie dargestellt, mit der alle Blockblobs gelöscht werden, die mit `Project = Contoso` gekennzeichnet sind. Erfahren Sie mehr über den Blobindex unter [Verwalten und Suchen von Daten in Azure Blob Storage mit dem Blobindex (Vorschau)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Verwalten von Versionen

Für Daten, die während ihrer gesamten Lebensdauer regelmäßig geändert werden und auf die regelmäßig zugegriffen wird, können Sie die Blob Storage-Versionsverwaltung aktivieren, um frühere Versionen eines Objekts automatisch zu verwalten. Sie können eine Richtlinie erstellen, um frühere Versionen Ebenen zuzuordnen oder zu löschen. Das Alter der Version wird durch Auswertung der Erstellungszeit der Version bestimmt. Entsprechend dieser Richtlinienregel werden frühere Versionen innerhalb des Containers `activedata`, die 90 Tage oder älter sind (nach der Versionserstellung), der Ebene „kalt“ zugeordnet und frühere Versionen, die 365 Tage oder älter sind, gelöscht.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png)              | ![Ja](../media/icons/yes-icon.png) |
| Premium-Blockblobs          | ![Ja](../media/icons/yes-icon.png)|![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

## <a name="regional-availability-and-pricing"></a>Regionale Verfügbarkeit und Preise

Die Funktion zur Lebenszyklusverwaltung ist in allen Azure-Regionen verfügbar.

Richtlinien für die Lebenszyklusverwaltung sind kostenlos. Kunden werden die regulären Betriebskosten für die [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blobebene festlegen)-API-Aufrufe in Rechnung gestellt. Löschvorgänge sind kostenfrei.

Jede Aktualisierung der letzten Zugriffszeit auf einen Blob wird unter der Kategorie [andere Vorgänge](https://azure.microsoft.com/pricing/details/storage/blobs/) abgerechnet.

Weitere Informationen zu den Preisen finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Häufig gestellte Fragen

**Ich habe eine neue Richtlinie erstellt. Warum werden die Aktionen nicht sofort ausgeführt?**

Die Plattform führt die Lebenszyklusrichtlinie ein Mal täglich aus. Nachdem Sie eine neue Richtlinie konfiguriert haben, kann es bis zu 24 Stunden dauern, bis einige Aktionen das erste Mal ausgeführt werden.

**Wie lange dauert es nach dem Aktualisieren einer vorhandenen Richtlinie, bis die Aktionen ausgeführt werden?**

Es dauert bis zu 24 Stunden, bis die aktualisierte Richtlinie in Kraft tritt. Sobald die Richtlinie gültig ist, kann es bis zu 24 Stunden dauern, bis die Aktionen ausgeführt werden. Daher kann der Abschluss von Richtlinieaktionen bis zu 48 Stunden dauern. Wenn das Update eine Regel deaktivieren oder löschen soll und „enableAutoTierToHotFromCool“ verwendet wurde, wird dennoch das automatische Tiering auf die heiße Speicherebene durchgeführt. Legen Sie beispielsweise basierend auf dem letzten Zugriff eine Regel einschließlich „enableAutoTierToHotFromCool“ fest. Wenn die Regel deaktiviert oder gelöscht wird, sich ein Blob derzeit auf der kalten Speicherebene befindet und dann darauf zugegriffen wird, wird er wieder auf die heiße Speicherebene zurückgesetzt, da diese für den Zugriff außerhalb der Lebenszyklusverwaltung verwendet wird. Das Blob wird dann nicht von der heißen auf die kalte Speicherebene verschoben, da die Regel für die Lebenszyklusverwaltung deaktiviert bzw. gelöscht wurde. Die einzige Möglichkeit, „autoTierToHotFromCool“ zu verhindern, besteht darin, die Nachverfolgung des letzten Zugriffszeitpunkts zu deaktivieren.

**Ich habe ein archiviertes Blob manuell wieder aktiviert. Wie kann ich vorübergehend verhindern, dass es zurück auf die Archivspeicherebene verschoben wird?**

Wenn ein Blob von einer Zugriffsebene auf eine andere verschoben wird, ändert sich der Zeitpunkt der letzten Änderung nicht. Wenn Sie ein archiviertes Blob auf der heißen Ebene manuell aktivieren, würde es vom Modul für die Lebenszyklusverwaltung zurück auf die Archivspeicherebene verschoben. Deaktivieren Sie vorübergehend die Regel, die sich auf dieses Blob auswirkt, um zu verhindern, dass es erneut archiviert wird. Aktivieren Sie die Regel erneut, wenn das Blob sicher zurück auf die Archivspeicherebene verschoben werden kann. Sie können das Blob auch an einen anderen Speicherort kopieren, falls es sich dauerhaft auf einer heißen oder kalten Ebene befinden muss.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren einer Richtlinie für die Lebenszyklusverwaltung](lifecycle-management-policy-configure.md)
- [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](access-tiers-overview.md)
- [Verwalten und Suchen von Daten in Azure Blob Storage mit dem Blobindex](storage-manage-find-blobs.md)
