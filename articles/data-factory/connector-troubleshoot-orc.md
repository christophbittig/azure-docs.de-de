---
title: Behandeln von Problemen mit dem ORC-Format-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem ORC-Format-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 973322c2ec7747fc5233eb897b302bfc910fee1b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390555"
---
# <a name="troubleshoot-the-orc-format-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung mit dem ORC-Format-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem XML-Format-Connector in Azure Data Factory und Azure Synapse.

## <a name="error-code-orcjavainvocationexception"></a>Fehlercode: OrcJavaInvocationException

- **Meldung**: `An error occurred when invoking Java, message: %javaException;.`
- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“, „Java heap space“ und „doubleCapacity“ enthält, liegt normalerweise ein Speicherverwaltungsproblem in einer alten Version der Integration Runtime vor. | Wenn Sie die selbstgehostete Integration Runtime verwenden, empfiehlt es sich, ein Upgrade auf die neueste Version durchzuführen. |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.OutOfMemory“ enthält, verfügt die Integration Runtime nicht über genügend Ressourcen zum Verarbeiten der Dateien. | Begrenzen Sie die gleichzeitigen Ausführungen auf die Integration Runtime. Skalieren Sie für „Selbstgehostete IR“ auf einen leistungsfähigen Computer mit einem Arbeitsspeicher von mindestens 8 GB hoch. |
    |Wenn die Fehlermeldung die Zeichenfolge „NullPointerReference“ enthält, ist die Ursache möglicherweise ein vorübergehender Fehler. | Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |
    | Wenn die Fehlermeldung die Zeichenfolge „BufferOverflowException“ enthält, ist die Ursache möglicherweise ein vorübergehender Fehler. | Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |
    | Wenn die Fehlermeldung die Zeichenfolge „java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable cannot be cast to org.apache.hadoop.io.Text“ enthält, ist die Ursache möglicherweise ein Typkonvertierungsproblem in der Java-Runtime. Normalerweise bedeutet dies, dass die Quelldaten in der Java-Laufzeit nicht ordnungsgemäß verarbeitet werden können. | Dies ist ein Datenproblem. Versuchen Sie, „string“ anstelle von „char“ oder „varchar“ in den Daten im ORC-Format zu verwenden. |

## <a name="error-code-orcdatetimeexceedlimit"></a>Fehlercode: OrcDateTimeExceedLimit

- **Meldung**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Ursache:** Wenn der DateTime-Wert „0001-01-01 00:00:00“ lautet, kann dies durch den Unterschied zwischen [julianischem Kalender und gregorianischem Kalender](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates) verursacht werden.

- **Empfehlung**:  Überprüfen Sie den Taktwert, und vermeiden Sie den DateTime-Wert „0001-01-01 00:00:00“.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
