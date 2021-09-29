---
title: Erstellen eines Zuordnungsdatenflusses
description: Erfahren Sie, wie Sie einen Datenfluss mit Azure Data Factory Mapping Data Flow erstellen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 83b40121de72dc45582ded94580154f5124817b0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061008"
---
# <a name="create-azure-data-factory-data-flow"></a>Erstellen eines Azure Data Factory-Datenflusses

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Mit Mapping Data Flow in ADF können Sie Daten nach Maß transformieren, ohne Code schreiben zu müssen. Sie können einen Datentransformationsauftrag im Datenfluss-Designer entwerfen, indem Sie eine Reihe von Transformationen erstellen. Beginnen Sie mit einer beliebigen Anzahl von Quelltransformationen, gefolgt von Datentransformationsschritten. Vervollständigen Sie dann Ihren Datenfluss mit einer Senke, damit Ihre Ergebnisse an ein Ziel gelangen.

Erstellen Sie zuerst im Azure-Portal eine neue Data Factory (V2). Wählen Sie nach dem Erstellen der neuen Factory in der Kachel „Open Azure Data Factory Studio“ (Azure Data Factory Studio öffnen) die Option „Öffnen“ aus, um die Data Factory-Benutzeroberfläche zu starten.

:::image type="content" source="media/data-flow/v2portal.png" alt-text="Screenshot: Bereich „Neue Data Factory“, in dem als Version „V2“ ausgewählt ist":::

Sobald Sie sich auf der Data Factory-Benutzeroberfläche befinden, können Sie die Beispieldatenflüsse verwenden. Die Beispiele sind im ADF-Vorlagenkatalog verfügbar. Wählen Sie in ADF im Abschnitt „Weitere Informationen“ der Startseite die Kachel „Pipelinevorlagen“ aus, und wählen Sie im Vorlagenkatalog die Kategorie „Datenfluss“ aus.

:::image type="content" source="media/data-flow/template.png" alt-text="Screenshot: Registerkarte „Datenfluss“, auf der „Transform data using data flow“ (Daten mithilfe des Datenflusses transformieren) ausgewählt ist":::

Sie werden zur Eingabe Ihrer Azure Blob Storage-Kontoinformationen aufgefordert.

:::image type="content" source="media/data-flow/template2.png" alt-text="Screenshot: Bereich „Transform data using data flow“ (Daten mithilfe des Datenflusses transformieren), in dem Sie Benutzereingaben eingeben können":::

[Die für diese Beispiele verwendeten Daten finden Sie hier](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Laden Sie die Beispieldaten herunter, und speichern Sie die Dateien in Ihren Azure Blob Storage-Konten, damit Sie die Beispiele ausführen können.

## <a name="create-new-data-flow"></a>Erstellen des neuen Datenflusses

Verwenden Sie auf der ADF-Benutzeroberfläche die Schaltfläche „Ressource erstellen“ mit dem Pluszeichen (+), um Datenflüsse zu erstellen.

:::image type="content" source="media/data-flow/newresource.png" alt-text="Screenshot: Option „Datenfluss“, die im Menü „Factory Resources“ (Factoryressourcen) ausgewählt ist":::

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie die Erstellung Ihrer Datentransformation mit einer [Quelltransformation](data-flow-source.md).
