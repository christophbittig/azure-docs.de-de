---
title: Grundlegendes zu Ressourcensätzen
description: In diesem Artikel wird erläutert, was Ressourcensätze sind und wie Azure Purview sie erstellt.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: 4e15f2813a0aea04f63cc11b3273c4a91d44afb2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845872"
---
# <a name="understanding-resource-sets"></a>Grundlegendes zu Ressourcensätzen

In diesem Artikel erfahren Sie, wie Azure Purview Datenressourcen mit Ressourcensätzen logischen Ressourcen zuordnet.

## <a name="background-info"></a>Hintergrundinformationen

In skalierbaren Datenverarbeitungssystemen wird eine einzelne Tabelle in der Regel in mehreren Dateien gespeichert. Im Azure Purview-Datenkatalog geschieht dies mithilfe von Ressourcensätzen. Ein Ressourcensatz ist ein einzelnes Objekt im Katalog, das eine große Anzahl von Ressourcen im Speicher darstellt.

Angenommen, Ihr Spark-Cluster hat einen DataFrame in einer Azure Data Lake Storage Gen2-Datenquelle (ADLS) persistent gespeichert. In Spark sieht die Tabelle zwar wie eine einzelne logische Ressource aus, aber auf dem Datenträger gibt es wahrscheinlich Tausende von Parquet-Dateien, von denen jede eine Partition des gesamten DataFrame-Inhalts darstellt. Für IoT-Daten und Webprotokolldaten gilt dieselbe Herausforderung. Stellen Sie sich vor, Sie verfügen über einen Sensor, der mehrmals pro Sekunde Protokolldateien ausgibt. Es dauert nicht lange, bis Hunderttausende von Protokolldateien dieses einzelnen Sensors vorliegen.

## <a name="how-azure-purview-detects-resource-sets"></a>So erkennt Azure Purview Ressourcensätze

Azure Purview unterstützt das Erkennen von Ressourcensätzen in Azure Blob Storage, ADLS Gen1, ADLS Gen2, Azure Files und Amazon S3.

Azure Purview erkennt beim Scannen automatisch Ressourcensätze. Dieses Feature untersucht alle Daten, die in der Überprüfung erfasst wurden, und vergleicht sie mit einem Satz von definierten Mustern.

Angenommen, Sie scannen eine Datenquelle, deren URL `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` ist. Azure Purview prüft die Pfadsegmente und bestimmt, ob sie mit den integrierten Mustern identisch sind. Integrierte Muster für GUIDs, Zahlen, Datumsformate, Lokalisierungscodes (z. B. „en-us“) usw. sind verfügbar. In diesem Fall entspricht das Zahlenmuster *23*. Azure Purview geht davon aus, dass diese Datei Teil eines Ressourcensatzes namens `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` ist.

Für eine URL wie `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` findet Azure Purview sowohl das Lokalisierungsmuster als auch das Zahlenmuster, wobei ein Ressourcensatz mit dem Namen `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` erzeugt wird.

Mit dieser Strategie ordnet Azure Purview die folgenden Ressourcen dem gleichen Ressourcensatz `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` zu:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

### <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Dateitypen, die von Azure Purview nicht als Ressourcensätze erkannt werden

Purview versucht absichtlich nicht, die meisten Dokumentdateitypen wie Word, Excel oder PDF als Ressourcensätze zu klassifizieren. Das CSV-Format bildet eine Ausnahme, da es sich dabei um ein gängiges partitioniertes Dateiformat handelt.

## <a name="how-azure-purview-scans-resource-sets"></a>So überprüft Azure Purview Ressourcensätze

Wenn Azure Purview Ressourcen erkennt, die als Teil eines Ressourcensatzes eingeschätzt werden, erfolgt der Wechsel von der vollständigen Überprüfung zur Stichprobenüberprüfung. Bei einer Stichprobenüberprüfung wird nur eine Teilmenge der Dateien geöffnet, die im Ressourcensatz vermutet werden. Dabei werden für jede geöffnete Datei das zugehörige Schema verwendet und die jeweiligen Klassifizierungen ausgeführt. Azure Purview findet dann die neueste Ressource unter den geöffneten Ressourcen und verwendet das Schema und die Klassifizierungen dieser Ressource im Eintrag für den gesamten Ressourcensatz im Katalog.

## <a name="advanced-resource-sets"></a>Erweiterte Ressourcensätze

Standardmäßig bestimmt Azure Purview das Schema und die Klassifizierungen für Ressourcensätze basierend auf den [Stichprobenregeln für Ressourcensatzüberprüfungen](sources-and-scans.md#resource-set-file-sampling). Azure Purview kann die Ressourcen Ihres Ressourcensatzes mithilfe der Funktion **Erweiterte Ressourcensätze** anpassen und erweitern. Ist die Option „Erweiterte Ressourcensätze“ aktiviert, führt Azure Purview zusätzliche Aggregationen aus, um die folgenden Informationen zu den Ressourcen eines Ressourcensatzes zu berechnen:

- Die meisten aktuellen Schemas und Klassifizierungen für eine genaue Darstellung der Schemaabweichung durch veränderte Metadaten
- Einen Beispielpfad einer Datei, die im Ressourcensatz enthalten ist
- Eine Partitionsanzahl mit der Anzahl der Dateien, die den Ressourcensatz bilden 
- Eine Schemaanzahl mit der Anzahl der eindeutigen Schemas, die gefunden wurden Dieser Wert ist entweder eine Zahl von 1–5 oder 5+ für Werte größer als 5.
- Eine Liste von Partitionstypen, wenn mehrere Partitionstypen im Ressourcensatz enthalten sind. Beispielsweise könnte ein IoT-Sensor sowohl XML- als auch JSON-Dateien ausgeben, obwohl beide logisch Teil desselben Ressourcensatzes sind.
- Die Gesamtgröße aller Dateien, aus denen der Ressourcensatz besteht 

Diese Eigenschaften finden Sie auf der Seite mit den Ressourcendetails des Ressourcensatzes.

:::image type="content" source="media/concept-resource-sets/resource-set-properties.png" alt-text="Berechnete Eigenschaften bei aktivierter Option „Erweiterte Ressourcensätze“" border="true":::

Wenn Sie die Option „Erweiterte Ressourcensätze“ aktivieren, können Sie auch [Musterregeln für Ressourcensätze](how-to-resource-set-pattern-rules.md) erstellen. Damit können Sie festlegen, wie Azure Purview die Ressourcensätze während der Überprüfung gruppieren soll. 

### <a name="turning-on-advanced-resource-sets"></a>Aktivieren erweiterter Ressourcensätze

Die Option „Erweiterte Ressourcensätze“ ist in allen neuen Azure Purview-Instanzen standardmäßig deaktiviert. Wenn Sie die Option aktivieren möchten, wechseln Sie im Verwaltungshub zu **Kontoinformationen**.

:::image type="content" source="media/concept-resource-sets/advanced-resource-set-toggle.png" alt-text="Aktivieren der Option „Erweiterte Ressourcensätze“" border="true":::

Sobald Sie die erweiterten Ressourcensätze aktiviert haben, werden für alle neu erfassten Ressourcen die zusätzlichen Erweiterungen ausgeführt. Es wird empfohlen, nach Aktivierung des Features eine Stunde zu warten, bevor Sie neue Data Lake-Daten überprüfen.

> [!IMPORTANT]
> Das Aktivieren erweiterter Ressourcensätze wirkt sich auf die Aktualisierungsrate von Ressourcen- und Klassifizierungserkenntnissen aus. Bei aktivierter Option „Erweiterte Ressourcensätze“ werden Ressourcen- und Klassifizierungserkenntnisse nur zweimal täglich aktualisiert.

## <a name="built-in-resource-set-patterns"></a>Integrierte Ressourcensatzmuster

Azure Purview unterstützt die folgenden Ressourcensatzmuster. Diese Muster können als Name in einem Verzeichnis oder als Teil eines Dateinamens angezeigt werden.
### <a name="regex-based-patterns"></a>Auf Regex basierende Muster

| Mustername | Anzeigename | BESCHREIBUNG |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Ein global eindeutiger Bezeichner wie in [RFC 4122](https://tools.ietf.org/html/rfc4122) definiert. |
| Number       | {N}          | Mindestens eine Dezimalstelle. |
| Datums-/Uhrzeitformate | {Jahr}{Monat}{Tag}{N}     | Wir unterstützen verschiedene Datums-/Uhrzeitformate, aber alle Formate werden als {Jahr}[Trennzeichen]{Monat}[Trennzeichen]{Tag} oder Reihe von {N}s dargestellt. |
| 4ByteHex     | {HEX}        | Eine vierstellige Hexadezimalzahl. |
| Lokalisierung | {LOC}        | Ein Sprachtag, wie in [BCP 47](https://tools.ietf.org/html/bcp47) definiert. Namen mit den Zeichen - sowie _ werden unterstützt (z. B. en_ca und en-ca). |

### <a name="complex-patterns"></a>Komplexe Muster

| Mustername | Anzeigename | BESCHREIBUNG |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Dateibezeichner der Spark-Partition |
| Date(jjjj/mm/tt)InPath  | {Jahr}/{Monat}/{Tag} | Muster für Jahr/Monat/Tag, das sich über mehrere Ordner erstreckt |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-data-catalog"></a>Anzeigen von Ressourcensätzen im Azure Purview-Datenkatalog

Wenn Azure Purview eine Gruppe von Ressourcen einem Ressourcensatz zuordnet, wird versucht, die nützlichsten Informationen zu extrahieren, um sie als Anzeigenamen im Katalog zu verwenden. Einige Beispiele für die standardmäßig angewendete Benennungskonvention: 

### <a name="example-1"></a>Beispiel 1

Qualifizierter Name: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Anzeigename: „name of spark output“(Name der Spark-Ausgabe)

### <a name="example-2"></a>Beispiel 2

Qualifizierter Name: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Anzeigename: „my partitioned data“ (meine partitionierten Daten)

### <a name="example-3"></a>Beispiel 3

Qualifizierter Name: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Anzeigename: „data“ (Daten)

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Anpassen der Ressourcengruppengruppe mithilfe von Musterregeln

Beim Überprüfen eines Speicherkontos verwendet Azure Purview eine Reihe definierter Muster, um zu bestimmen, ob eine Gruppe von Ressourcen ein Ressourcensatz ist. In einigen Fällen entspricht die Ressourcensatzgruppierung von Azure Purview möglicherweise nicht genau dem Datentyp. Diese Probleme können folgendes umfassen:

- Fälschliche Kennzeichnung einer Ressource als Ressourcensatz
- Einfügen einer Ressource in den falschen Ressourcensatz
- Fälschliche Kennzeichnung einer Ressource als kein Ressourcensatz

Sie können im Management Center Musterregeln definieren, um anzupassen oder außer Kraft zu setzen, wie Azure Purview erkennt, welche Assets als Ressourcensets gruppiert sind und wie sie innerhalb des Katalogs angezeigt werden. Eine Schritt-für-Schritt-Anleitung und Syntax finden Sie unter [Musterregeln für Ressourcengruppen](how-to-resource-set-pattern-rules.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Purview finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
