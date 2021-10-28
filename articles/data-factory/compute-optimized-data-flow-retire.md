---
title: Option „Für Compute optimiert“ wird eingestellt
description: Die Datenflussoption „Für Compute optimiert“ wird eingestellt.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: tutorial
ms.date: 06/29/2021
ms.openlocfilehash: 781841ff8a65d36a9ed712efa5d0bdf8480995bf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229368"
---
# <a name="retirement-of-data-flow-compute-optimized-option"></a>Datenflussoption „Für Compute optimiert“ wird eingestellt

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory- und Azure Synapse Analytics-Datenflüsse bieten einen Mechanismus zum Transformieren von Daten in ETL-Aufträgen im großen Stil mithilfe eines Paradigmas des grafischen Designs – dabei ist nur wenig Code erforderlich. Datenflüsse werden in der serverlosen Integration Runtime-Infrastruktur von Azure Data Factory und Azure Synapse Analytics ausgeführt. Aufgrund der skalierbaren Natur der Integration Runtimes von Azure Data Factory und Azure Synapse Analytics gab es drei verschiedene Computeoptionen für die Azure Databricks Spark-Umgebung, die zum Ausführen von Datenflüssen im großen Stil verwendet wird: „Arbeitsspeicheroptimiert“, „Universell“ und „Für Compute optimiert“. Für Produktionsworkloads sind „Arbeitsspeicheroptimiert“ und „Universell“ die empfohlenen Klassen für das Datenflusscomputing mit Ihrer Integration Runtime. Da „Für Compute optimiert“ für allgemeine Anwendungsfälle mit Datenflüssen häufig nicht ausreicht, empfiehlt sich die Verwendung von universellen oder arbeitsspeicheroptimierten Datenflüssen in Produktionsworkloads.

## <a name="migration-steps"></a>Schritte bei der Migration

Bis einschließlich 31. August 2024 funktionieren Ihre für Compute optimierten Datenflüsse weiterhin in Ihren vorhandenen Pipelines. Um eine Unterbrechung Ihrer Dienste zu vermeiden, entfernen Sie vor dem 31. August 2024 alle für Compute optimierten Datenflüsse, und führen Sie die unten beschriebenen Schritte aus, um eine neue Azure Integration Runtime und eine neue Datenflussaktivität zu erstellen. Gehen Sie zum Erstellen einer neuen Datenflussaktivität folgendermaßen vor:

1. Erstellen Sie eine neue Azure Integration Runtime mit dem Computetyp „Universell“ oder „Arbeitsspeicheroptimiert“.
2. Legen Sie Ihre Datenflussaktivität auf einen dieser Computetypen fest.

   :::image type="content" source="media/data-flow/compute-types.png" alt-text="Computetypen":::

## <a name="comparison-between-different-compute-options"></a>Vergleich zwischen verschiedenen Computeoptionen 

| Computeoption              | Leistung                                                  |
| :-------------------- | :----------------------------------------------------------- |
| Universelle Datenflüsse (Basic) | Gut geeignet für allgemeine Anwendungsfälle in Produktionsworkloads |
| Arbeitsspeicheroptimierte Datenflüsse (Standard) | Runtime mit der besten Leistung für Datenflüsse bei großen Datasets und vielen Berechnungen |
| Für Compute optimierte Datenflüsse (veraltet) | Für Produktionsworkloads nicht empfohlen |

* [Aktuelle Informationen zu den Preisen für universelle und arbeitsspeicheroptimierte Datenflüsse finden Sie auf der Preisseite für Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
* [Detaillierte Informationen finden Sie in den häufig gestellten Fragen zu Datenflüssen.](./frequently-asked-questions.yml#mapping-data-flows)  
* [Auf der Microsoft Q&A-Seite können Sie Fragen posten und Antworten finden.](https://aka.ms/datafactoryqa)