---
title: Mehrere Verzweigungen im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Replizieren von Datenströmen im Zuordnungsdatenfluss mit mehreren Verzweigungen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 05aa62c32054f7a5e9ab75367f3d07c6f9223506
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060022"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Erstellen einer neuen Verzweigung im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Fügen Sie eine neue Verzweigung hinzu, um mehrere Vorgänge und Transformationen für denselben Datenstrom auszuführen. Das Hinzufügen einer neuen Verzweigung ist nützlich, wenn Sie dieselbe Quelle für mehrere Senken oder für Selbstverknüpfungsdaten zusammen verwenden möchten.

Eine neue Verzweigung kann ähnlich wie andere Transformationen aus der Transformationsliste hinzugefügt werden. **Neue Verzweigung** ist als Aktion nur dann verfügbar, wenn es eine Transformation gibt, die auf die zu verzweigende Transformation folgt.

:::image type="content" source="media/data-flow/new-branch2.png" alt-text="Screenshot: Option „Neuer Branch“ im Menü „Multiple inputs/outputs“ (Mehrere Eingaben/Ausgaben)":::

Im nachstehenden Beispiel liest der Datenfluss die Daten einer Taxifahrt. Dazu ist die aus „Day“ (Tag )und „Vendor“ (Anbieter) aggregierte Ausgabe erforderlich. Statt zwei separate Datenflüsse zu erstellen, die aus derselben Quelle gelesen werden, kann eine neue Verzweigung hinzugefügt werden. Auf diese Weise können beide Aggregationen als Teil desselben Datenflusses ausgeführt werden. 

:::image type="content" source="media/data-flow/new-branch.png" alt-text="Screenshot: Datenfluss mit zwei Branches aus der Quelle":::

> [!NOTE]
> Wenn Sie auf das Pluszeichen (+) klicken, um Ihrem Graphen Transformationen hinzuzufügen, wird die Option „Neuer Branch“ nur angezeigt, wenn nachfolgende Transformationsblöcke vorhanden sind. Das liegt daran, dass durch „Neuer Branch“ ein Verweis auf den vorhandenen Stream erstellt wird und eine weitere Upstreamverarbeitung erforderlich ist. Sollte die Option „Neuer Branch“ nicht angezeigt werden, fügen Sie zuerst eine abgeleitete Spalte oder eine andere Transformation hinzu. Wenn Sie nun zum vorherigen Block zurückkehren, wird „Neuer Branch“ als Option angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Branchen können Sie die [Datenflusstransformationen](data-flow-transformation-overview.md) verwenden.
