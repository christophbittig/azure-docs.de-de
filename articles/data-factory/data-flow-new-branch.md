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
ms.date: 04/16/2021
ms.openlocfilehash: e84b1d1a2130c2d1c621ade1b21f92c485709100
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639990"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Erstellen einer neuen Verzweigung im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Fügen Sie eine neue Verzweigung hinzu, um mehrere Vorgänge und Transformationen für denselben Datenstrom auszuführen. Das Hinzufügen einer neuen Verzweigung ist nützlich, wenn Sie dieselbe Quelle für mehrere Senken oder für Selbstverknüpfungsdaten zusammen verwenden möchten.

Eine neue Verzweigung kann ähnlich wie andere Transformationen aus der Transformationsliste hinzugefügt werden. **Neue Verzweigung** ist als Aktion nur dann verfügbar, wenn es eine Transformation gibt, die auf die zu verzweigende Transformation folgt.

![Screenshot: Option „Neuer Branch“ im Menü „Multiple inputs/outputs“ (Mehrere Eingaben/Ausgaben)](media/data-flow/new-branch2.png "Hinzufügen einer neuen Verzweigung")

Im nachstehenden Beispiel liest der Datenfluss die Daten einer Taxifahrt. Dazu ist die aus „Day“ (Tag )und „Vendor“ (Anbieter) aggregierte Ausgabe erforderlich. Statt zwei separate Datenflüsse zu erstellen, die aus derselben Quelle gelesen werden, kann eine neue Verzweigung hinzugefügt werden. Auf diese Weise können beide Aggregationen als Teil desselben Datenflusses ausgeführt werden. 

![Screenshot: Datenfluss mit zwei Branches aus der Quelle](media/data-flow/new-branch.png "Hinzufügen einer neuen Verzweigung")

> [!NOTE]
> Wenn Sie auf das Pluszeichen (+) klicken, um Ihrem Graphen Transformationen hinzuzufügen, wird die Option „Neuer Branch“ nur angezeigt, wenn nachfolgende Transformationsblöcke vorhanden sind. Das liegt daran, dass durch „Neuer Branch“ ein Verweis auf den vorhandenen Stream erstellt wird und eine weitere Upstreamverarbeitung erforderlich ist. Sollte die Option „Neuer Branch“ nicht angezeigt werden, fügen Sie zuerst eine abgeleitete Spalte oder eine andere Transformation hinzu. Wenn Sie nun zum vorherigen Block zurückkehren, wird „Neuer Branch“ als Option angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Branchen können Sie die [Datenflusstransformationen](data-flow-transformation-overview.md) verwenden.
