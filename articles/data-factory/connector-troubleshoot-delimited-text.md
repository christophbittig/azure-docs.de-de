---
title: Problembehandlung beim Connector von Textformaten mit Trennzeichen
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem Connector für Textformate mit Trennzeichen in Azure Data Factory und Azure Synapse Analytics beheben können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 42346a5922b0f607e467e495455166057248db5e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390551"
---
# <a name="troubleshoot-the-delimited-text-format-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung beim Connector von Textformaten mit Trennzeichen in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel bietet Vorschläge zur Behandlung von häufigen Problemen beim Connector von Textformaten mit Trennzeichen in Azure Data Factory und Azure Synapse.

## <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Fehlercode: DelimitedTextColumnNameNotAllowNull

- **Meldung**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Ursache:** Wenn „firstrowasheader“ in der Aktivität festgelegt ist, wird die erste Zeile als Spaltenname verwendet. Dieser Fehler bedeutet, dass die erste Zeile einen leeren Wert (z. B. „ColumnA, ColumnB) enthält.

- **Empfehlung**:  Überprüfen Sie die erste Zeile, und korrigieren Sie den Wert, wenn ein leerer Wert vorhanden ist.


## <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Fehlercode: DelimitedTextMoreColumnsThanDefined

- **Meldung**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

  | Ursachenanalyse                                               | Empfehlung                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Die Spaltenanzahl der problematischen Zeile ist größer als die Spaltenanzahl der ersten Zeile. Dies kann durch ein Datenproblem oder falsche Einstellungen für Spaltentrennzeichen oder Anführungszeichen verursacht werden. | Rufen Sie die Zeilenanzahl aus der Fehlermeldung ab, überprüfen Sie die Spalte in der Zeile, und korrigieren Sie die Daten. |
  | Wenn die erwartete Spaltenanzahl in einer Fehlermeldung „1“ lautet, haben Sie möglicherweise falsche Komprimierungs- oder Formateinstellungen angegeben, sodass die Dateien falsch analysiert wurden. | Überprüfen Sie die Formateinstellungen, um sicherzustellen, dass sie mit Ihren Quelldateien übereinstimmen. |
  | Wenn Ihre Quelle ein Ordner ist, weisen die Dateien im angegebenen Ordner möglicherweise ein anderes Schema auf. | Stellen Sie sicher, dass die Dateien im angegebenen Ordner ein identisches Schema aufweisen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
