---
title: Behandeln von Problemen mit dem XML-Format-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem XML-Format-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d4a6d8c26b9441663cf67a1ea9a837091a51a624
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390561"
---
# <a name="troubleshoot-the-xml-format-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den XML-Format-Connector in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem XML-Format-Connector in Azure Data Factory und Azure Synapse Analytics.

## <a name="error-code-xmlsinknotsupported"></a>Fehlercode: XmlSinkNotSupported

- **Meldung**: `Write data in XML format is not supported yet, choose a different format!`

- **Ursache:** Es wurde ein XML-Dataset als Senkendataset in der Kopieraktivität verwendet.

- **Empfehlung**:  Verwenden Sie ein Dataset in einem anderen Format als das Senkendataset.


## <a name="error-code-xmlattributecolumnnameconflict"></a>Fehlercode: XmlAttributeColumnNameConflict

- **Meldung**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Ursache:** Es wurde ein Attributpräfix verwendet, das den Konflikt verursacht hat.

- **Empfehlung**:  Legen Sie einen anderen Wert für die Eigenschaft „attributePrefix“ fest.


## <a name="error-code-xmlvaluecolumnnameconflict"></a>Fehlercode: XmlValueColumnNameConflict

- **Meldung**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Ursache:** Es wurde einer der untergeordneten Elementnamen als Spaltenname für den Elementwert verwendet.

- **Empfehlung**:  Legen Sie einen anderen Wert für die Eigenschaft „valueColumn“ fest.


## <a name="error-code-xmlinvalid"></a>Fehlercode: XmlInvalid

- **Meldung**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Ursache:** Die XML-Eingabedatei ist nicht wohlgeformt.

- **Empfehlung**:  Korrigieren Sie die XML-Datei, damit sie wohlgeformt ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
