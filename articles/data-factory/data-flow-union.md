---
title: Vereinigungstransformation im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie mehr über die Transformation für neue Branches für Zuordnungsdatenflüsse in Azure Data Factory und Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 14cf10c51ffe470ff249ff4953f0edd8990aab1b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059680"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Vereinigungstransformation im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Union kombiniert mehrere Datenströme zu einem einzigen, wobei die SQL Union dieser Datenströme die neue Ausgabe der Union-Transformation ist. Alle Schemas aus jedem Eingabestream werden innerhalb Ihres Datenflusses kombiniert, ohne dass Sie einen Joinschlüssel benötigen.

Sie können die n-Anzahl von Streams in der Einstellungstabelle kombinieren, indem Sie neben jeder konfigurierten Zeile auf das Symbol „+“ klicken, einschließlich sowohl Quelldaten als auch Streams aus vorhandenen Transformationen in Ihrem Datenfluss.

In diesem kurzen Video sehen Sie eine exemplarische Vorgehensweise für eine Vereinigungstransformation im Zuordnungsdatenfluss:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

:::image type="content" source="media/data-flow/union.png" alt-text="Vereinigungstransformation":::

In diesem Fall können Sie unterschiedliche Metadaten aus mehreren Quellen (in diesem Beispiel drei verschiedene Quelldateien) zusammenführen und zu einem einzigen Stream kombinieren:

:::image type="content" source="media/data-flow/union111.png" alt-text="Vereinigungstransformation: Übersicht":::

Um dies zu erreichen, fügen Sie zusätzliche Zeilen in „Union Settings“ (Vereinigungseinstellungen) hinzu, indem Sie alle Quellen einbeziehen, die Sie hinzufügen möchten. Ein allgemeiner Verweis oder Joinschlüssel ist nicht notwendig:

:::image type="content" source="media/data-flow/unionsettings.png" alt-text="Vereinigungstransformation: Einstellungen":::

Wenn Sie nach Ihrer Vereinigung eine Auswahltransformation festlegen, können Sie überlappende Felder oder Felder umbenennen, die in Quellen ohne Kopfzeilen nicht benannt wurden. Klicken Sie auf „Inspekt“ (Überprüfen), um die kombinierten Metadaten mit insgesamt 132 Spalten in diesem Beispiel aus drei verschiedenen Quellen anzuzeigen:

:::image type="content" source="media/data-flow/union333.png" alt-text="Endgültige Vereinigungstransformation":::

## <a name="name-and-position"></a>Name und Position

Wenn Sie „Union by name“ (Vereinigung anhand des Namens) wählen, wird jeder Spaltenwert mit einem neuen verketteten Metadatenschema aus jeder Quelle in die entsprechende Spalte übernommen.

Wenn Sie "Union by position" (Vereinigung anhand der Position) wählen, wird jeder Spaltenwert aus jeder entsprechenden Quelle an der ursprünglichen Position abgelegt, was zu einem neuen kombinierten Stream aus Daten führt, bei dem die Daten aus jeder Quelle zum gleichen Stream hinzugefügt werden:

:::image type="content" source="media/data-flow/unionoutput.png" alt-text="Vereinigungsausgabe":::

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie ähnliche Transformationen, einschließlich [Join](data-flow-join.md) und [Exists](data-flow-exists.md).
