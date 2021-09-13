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
ms.openlocfilehash: 1ab0a225cc38290ef8f42eb8375ddc2f1f67c677
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355299"
---
# <a name="create-azure-data-factory-data-flow"></a>Erstellen eines Azure Data Factory-Datenflusses

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mit Mapping Data Flow in ADF können Sie Daten nach Maß transformieren, ohne Code schreiben zu müssen. Sie können einen Datentransformationsauftrag im Datenfluss-Designer entwerfen, indem Sie eine Reihe von Transformationen erstellen. Beginnen Sie mit einer beliebigen Anzahl von Quelltransformationen, gefolgt von Datentransformationsschritten. Vervollständigen Sie dann Ihren Datenfluss mit einer Senke, damit Ihre Ergebnisse an ein Ziel gelangen.

Erstellen Sie zuerst im Azure-Portal eine neue Data Factory (V2). Wählen Sie nach dem Erstellen der neuen Factory in der Kachel „Open Azure Data Factory Studio“ (Azure Data Factory Studio öffnen) die Option „Öffnen“ aus, um die Data Factory-Benutzeroberfläche zu starten.

![Screenshot: Bereich „Neue Data Factory“, in dem als Version „V2“ ausgewählt ist](media/data-flow/v2portal.png "Erstellen eines Datenflusses")

Sobald Sie sich auf der Data Factory-Benutzeroberfläche befinden, können Sie die Beispieldatenflüsse verwenden. Die Beispiele sind im ADF-Vorlagenkatalog verfügbar. Wählen Sie in ADF im Abschnitt „Weitere Informationen“ der Startseite die Kachel „Pipelinevorlagen“ aus, und wählen Sie im Vorlagenkatalog die Kategorie „Datenfluss“ aus.

![Screenshot: Registerkarte „Datenfluss“, auf der „Transform data using data flow“ (Daten mithilfe des Datenflusses transformieren) ausgewählt ist](media/data-flow/template.png "Erstellen eines Datenflusses")

Sie werden zur Eingabe Ihrer Azure Blob Storage-Kontoinformationen aufgefordert.

![Screenshot: Bereich „Transform data using data flow“ (Daten mithilfe des Datenflusses transformieren), in dem Sie Benutzereingaben eingeben können](media/data-flow/template2.png "Erstellen eines Datenflusses 2")

[Die für diese Beispiele verwendeten Daten finden Sie hier](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Laden Sie die Beispieldaten herunter, und speichern Sie die Dateien in Ihren Azure Blob Storage-Konten, damit Sie die Beispiele ausführen können.

## <a name="create-new-data-flow"></a>Erstellen des neuen Datenflusses

Verwenden Sie auf der ADF-Benutzeroberfläche die Schaltfläche „Ressource erstellen“ mit dem Pluszeichen (+), um Datenflüsse zu erstellen.

![Screenshot: Option „Datenfluss“, die im Menü „Factory Resources“ (Factoryressourcen) ausgewählt ist](media/data-flow/newresource.png "Neue Ressource")

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie die Erstellung Ihrer Datentransformation mit einer [Quelltransformation](data-flow-source.md).
