---
title: Problembehandlung für den Parquet-Format-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem Parquet-Format-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 64a49592609b2cb7fd262264bb9802de58db5f04
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390547"
---
# <a name="troubleshoot-the-parquet-format-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Parquet-Format-Connector in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Parquet-Format-Connector in Azure Data Factory und Azure Synapse Analytics.

## <a name="error-code-parquetjavainvocationexception"></a>Fehlercode: ParquetJavaInvocationException

- **Meldung**: `An error occurred when invoking java, message: %javaException;.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“, „Java heap space“ und „doubleCapacity“ enthält, liegt normalerweise ein Speicherverwaltungsproblem in einer alten Version der Integration Runtime vor. | Wenn Sie selbstgehostete IR verwenden und die Version älter als 3.20.7159.1 ist, wird ein Upgrade auf die neueste Version empfohlen. |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“ enthält, verfügt die Integration Runtime nicht über genügend Ressourcen zum Verarbeiten der Dateien. | Begrenzen Sie die gleichzeitigen Ausführungen auf die Integration Runtime. Skalieren Sie für „Selbstgehostete IR“ auf einen leistungsfähigen Computer mit einem Arbeitsspeicher von mindestens 8 GB hoch. |
    | Wenn die Fehlermeldung die Zeichenfolge „NullPointerReference“ enthält, handelt es sich möglicherweise um einem vorübergehenden Fehler. | Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |

## <a name="error-code-parquetinvalidfile"></a>Fehlercode: ParquetInvalidFile

- **Meldung**: `File is not a valid Parquet file.`

- **Ursache:** Dabei handelt es sich um ein Problem mit der Parquet-Datei.

- **Empfehlung**:  Überprüfen Sie, ob die Eingabe eine gültige Parquet-Datei ist.

## <a name="error-code-parquetnotsupportedtype"></a>Fehlercode: ParquetNotSupportedType

- **Meldung**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Ursache**: Das Parquet-Format wird in Azure Data Factory- und Synapse-Pipelines nicht unterstützt.

- **Empfehlung**: Überprüfen Sie die Quelldaten erneut. Verwenden Sie dazu die Informationen unter [Von der Copy-Aktivität unterstützte Dateiformate und Komprimierungscodecs](./supported-file-formats-and-compression-codecs.md).

## <a name="error-code-parquetmisseddecimalprecisionscale"></a>Fehlercode: ParquetMissedDecimalPrecisionScale

- **Meldung**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Ursache:** Die Zahlengenauigkeit und die Dezimalstellen wurden analysiert, doch diese Informationen wurden nicht bereitgestellt.

- **Empfehlung**:  Die Quelle gibt nicht die richtigen Genauigkeits- und Dezimalstelleninformationen zurück. Überprüfen Sie die Problemspalte auf diese Informationen.

## <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Fehlercode: ParquetInvalidDecimalPrecisionScale

- **Meldung**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Ursache:** Das Schema ist ungültig.

- **Empfehlung**:  Überprüfen Sie die Problemspalte auf Genauigkeit und Dezimalstellen.

## <a name="error-code-parquetcolumnnotfound"></a>Fehlercode: ParquetColumnNotFound

- **Meldung**: `Column %column; does not exist in Parquet file.`

- **Ursache:** Das Quellschema stimmt nicht mit dem Senkenschema überein.

- **Empfehlung**:  Überprüfen Sie die Zuordnungen in der Aktivität. Stellen Sie sicher, dass die Quellspalte der richtigen Senkenspalte zugeordnet werden kann.

## <a name="error-code-parquetinvaliddataformat"></a>Fehlercode: ParquetInvalidDataFormat

- **Meldung**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Ursache:** Die Daten können nicht in den in „mappings.source“ angegebenen Typ konvertiert werden.

- **Empfehlung**:  Überprüfen Sie die Quelldaten erneut, oder geben Sie den richtigen Datentyp für diese Spalte in der Spaltenzuordnung der Kopieraktivität an. Weitere Informationen finden Sie unter [Von der Copy-Aktivität unterstützte Dateiformate und Komprimierungscodecs](./supported-file-formats-and-compression-codecs.md).

## <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Fehlercode: ParquetDataCountNotMatchColumnCount

- **Meldung**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Ursache:**  Ein Konflikt zwischen der Anzahl der Quellspalten und der Anzahl der Senkenspalten.

- **Empfehlung**:  Überprüfen Sie erneut, ob die Anzahl der Quellspalten mit der Anzahl der Senkenspalten in „mapping“ übereinstimmt.

## <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Fehlercode: ParquetDataTypeNotMatchColumnType

- **Meldung**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Ursache:** Die Daten aus der Quelle können nicht in den Typ konvertiert werden, der in der Senke definiert ist.

- **Empfehlung**:  Geben Sie in „mapping.sink“ einen richtigen Typ an.

## <a name="error-code-parquetbridgeinvaliddata"></a>Fehlercode: ParquetBridgeInvalidData

- **Meldung**: `%message;`

- **Ursache:** Der Datenwert hat den Grenzwert überschritten.

- **Empfehlung**:  Wiederholen Sie den Vorgang. Wenden Sie sich an uns, wenn das Problem weiterhin besteht.

## <a name="error-code-parquetunsupportedinterpretation"></a>Fehlercode: ParquetUnsupportedInterpretation

- **Meldung**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Ursache:** Dieses Szenario wird nicht unterstützt.

- **Empfehlung**:  „ParquetInterpretFor“ sollte nicht „sparkSql“ sein.

## <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Fehlercode: ParquetUnsupportFileLevelCompressionOption

- **Meldung**: `File level compression is not supported for Parquet.`

- **Ursache:** Dieses Szenario wird nicht unterstützt.

- **Empfehlung**:  Entfernen Sie „CompressionType“ in der Nutzlast.

## <a name="error-code-usererrorjniexception"></a>Fehlercode: UserErrorJniException

- **Meldung**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Ursache:** Eine JVM-Instanz (Java Virtual Machine) kann nicht erstellt werden, da einige ungültige (globale) Argumente festgelegt wurden.

- **Empfehlung**:  Melden Sie sich bei dem Computer an, auf dem die *einzelnen Knoten* Ihrer selbstgehosteten IR gehostet werden. Stellen Sie wie folgt sicher, dass die Systemvariable ordnungsgemäß festgelegt ist: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`. Starten Sie alle IR-Knoten neu, und führen Sie dann die Pipeline erneut aus.

## <a name="arithmetic-overflow"></a>Arithmetischer Überlauf

- **Symptome:** Beim Kopieren von Parquet-Dateien ist eine Fehlermeldung aufgetreten: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Ursache:** Derzeit werden beim Kopieren von Dateien aus Oracle in das Parquet-Format nur Dezimalzahlen mit einer Genauigkeit <= 38 unterstützt, deren ganzzahliger Teil eine Länge <= 20 aufweist. 

- **Lösung:** Um dieses Problem zu umgehen, können Sie alle Spalten mit diesem Problem in VARCHAR2 konvertieren.

## <a name="no-enum-constant"></a>Keine Enumerationskonstante

- **Symptome:** Beim Kopieren von Daten in das Parquet-Format ist eine Fehlermeldung aufgetreten: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` oder `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`.

- **Ursache**: 

    Das Problem kann durch Leerzeichen oder nicht unterstützte Sonderzeichen (z. B. ,;{}()\n\t=) im Spaltennamen verursacht werden, weil dieses Format von Parquet nicht unterstützt wird. 

    Beispielsweise wird bei einem Spaltennamen wie *contoso(test)* der Typ in Klammern als [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` analysiert. Der Fehler wird ausgelöst, weil kein solcher Typ „test“ vorhanden ist.

    Informationen zu den unterstützten Typen finden Sie auf der GitHub-Website unter [apache/parquet-mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Lösung**: 

    Prüfen Sie erneut, ob Folgendes zutrifft:
    - Im Senkenspaltennamen sind Leerzeichen enthalten.
    - Die erste Spalte mit Leerzeichen wird als Spaltenname verwendet.
    - Der Typ „OriginalType“ wird unterstützt. Versuchen Sie, diese Sonderzeichen zu vermeiden: `,;{}()\n\t=`. 

## <a name="error-code-parquetdatetimeexceedlimit"></a>Fehlercode: ParquetDateTimeExceedLimit

- **Meldung**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Ursache:** Wenn der DateTime-Wert „0001-01-01 00:00:00“ lautet, kann dies durch den Unterschied zwischen julianischem Kalender und gregorianischem Kalender verursacht werden. Weitere Informationen finden Sie unter [Difference between Julian and proleptic Gregorian calendar dates](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates) (Unterschiede zwischen den Datumsangaben im julianischen und proleptischen gregorianischen Kalender).

- **Lösung**: Überprüfen Sie den Taktwert, und vermeiden Sie den DateTime-Wert „0001-01-01 00:00:00“.

## <a name="error-code-parquetinvalidcolumnname"></a>Fehlercode: ParquetInvalidColumnName

- **Meldung**: `The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **Ursache**: Der Spaltenname enthält ungültige Zeichen.

- **Lösung**: Fügen Sie die Spaltenzuordnung hinzu, oder ändern Sie sie, um den Namen der Senkenspalte gültig zu machen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
