---
title: Problembehandlung für den Oracle-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit dem Oracle-Connector in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0450faa94af3c01fccebc618c5f91094447116f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390522"
---
# <a name="troubleshoot-the-oracle-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für den Oracle-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit dem Oracle-Connector in Azure Data Factory und Azure Synapse.

## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Fehlercode: ArgumentOutOfRangeException

- **Meldung**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Ursache**: In Azure Data Factory- und Synapse-Pipelines werden DateTime-Werte im Bereich von 0001-01-01 00:00:00 bis 9999-12-31 23:59:59 unterstützt. Oracle unterstützt jedoch einen größeren Bereich von DateTime-Werten (z. B. Jahreszahlen vor unserer Zeitrechnung oder Minuten-/Sekundenangaben > 59). Dies kann zu einem Fehler führen.

- **Empfehlung**: 

    Führen Sie `select dump(<column name>)` aus, um zu ermitteln, ob sich der Wert in Oracle im unterstützten Datumsbereich befindet. 

    Wenn Sie die Bytereihenfolge im Ergebnis ermitteln möchten, lesen Sie [Wie werden Daten in Oracle gespeichert?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
