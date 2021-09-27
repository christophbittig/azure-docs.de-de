---
title: Unterschiede zu Azure Data Factory
description: Erfahren Sie, wie sich die Datenintegrationsfunktionen von Azure Synapse Analytics von denen in Azure Data Factory unterscheiden.
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: d8320c6356f310584d24c51a384446dfdcb22afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128608164"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Datenintegration in Azure Synapse Analytics im Vergleich zu Azure Data Factory

In Azure Synapse Analytics basieren die Datenintegrationsfunktionen wie Synapse-Pipelines und Datenflüsse auf denen von Azure Data Factory. Weitere Informationen finden Sie unter [Was ist Azure Data Factory?](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Verfügbare Funktionen in ADF und Azure Synapse Analytics

Sehen Sie in der folgenden Tabelle nach, welche Funktionen zur Verfügung stehen:

| Kategorie                 | Funktion    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Verwenden von SSIS und SSIS Integration Runtime | ✓ | ✗ |
|                          | Unterstützung für regionsübergreifende Integration Runtime (Datenflüsse) | ✓ | ✗ |
|                          | Integration Runtime-Freigabe | ✓<br><small>*Kann für verschiedene Data Factory-Instanzen freigegeben werden* | ✗ |
| **Azure Pipelines-Aktivitäten** | Aktivität „SSIS-Paket“ | ✓ | ✗ |
|                          | Unterstützung für Power Query-Aktivität | ✓ | ✗ |
| **Vorlagenkatalog und Knowledge Center** | Lösungsvorlagen | ✓<br><small>*Azure Data Factory-Vorlagenkatalog* | ✓<br><small>*Knowledge Center für den Synapse-Arbeitsbereich* |
| **Integration von Git-Repository** | Git-Integration | ✓ | ✓ |
| **Überwachung**           | Überwachen von Spark-Aufträgen für den Datenfluss | ✗ | ✓<br><small>*Nutzen der Synapse Spark-Pools* |

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit der Datenintegration in Ihrem Synapse-Arbeitsbereich, indem Sie lernen, wie Sie [Daten in einem Azure Data Lake Storage Gen2-Konto erfassen](data-integration-data-lake.md).
