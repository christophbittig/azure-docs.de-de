---
title: Power Query-Aktivität in Azure Data Factory
description: Erfahren Sie, wie Sie die Power Query-Aktivität für Data Wrangling-Funktionen in einer Data Factory-Pipeline verwenden.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 07e663b7b631cbc083916e48924d740255e46e37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638765"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Power Query-Aktivität in Azure Data Factory

Mit der Power Query-Aktivität können Sie Power Query-Mashups erstellen und ausführen, um so Data Wrangling im großen Stil in einer Data Factory-Pipeline vorzunehmen. Sie können ein neues Power Query-Mashup über die Menüoption „Neue Ressourcen“ erstellen oder durch Hinzufügen einer Power Query-Aktivität zur Pipeline.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="Screenshot mit Power Query im Bereich der Factoryressourcen":::

Zuvor wurde Data Wrangling in Azure Data Factory über die Menüoption „Datenfluss“ erstellt. Dies wurde in das Erstellen über eine neue Power Query-Aktivität geändert. Sie können direkt im Power Query-Mashup-Editor arbeiten, um eine interaktive Datenuntersuchung vorzunehmen und dann Ihre Arbeit zu speichern. Sobald der Vorgang beendet ist, können Sie die Power Query-Aktivität übernehmen und zu einer Pipeline hinzufügen. Azure Data Factory nimmt automatisch eine horizontale Hochskalierung vor und operationalisiert das Data Wrangling mithilfe der Spark-Umgebung für Datenflüsse in Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Übersetzung in Datenflussskript

Zur Skalierung für die Power Query-Aktivität übersetzt Azure Data Factory das ```M```-Skript in ein Datenflussskript, sodass Sie Power Query im großen Stil mithilfe der Spark-Umgebung für Azure Data Factory-Datenflüsse ausführen können. Erstellen Sie den Wranglingdatenfluss mithilfe der codefreien Datenvorbereitung. Die Liste der verfügbaren Funktionen finden Sie unter [Transformationsfunktionen](wrangling-functions.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Data Wrangling-Konzepte mithilfe von [Power Query in Azure Data Factory](wrangling-tutorial.md).
