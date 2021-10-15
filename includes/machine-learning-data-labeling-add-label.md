---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c85888c04859769d82b9c5ebe4691797e404adf
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368018"
---
Während des Datenbeschriftungsprozesses sollten Sie weitere Bezeichnungen hinzufügen, um Ihre Elemente zu klassifizieren.  So kann es beispielsweise sein, dass Sie für Unklarheiten Beschriftungen wie „Unbekannt“ oder „Sonstiges“ hinzufügen möchten.

Gehen Sie wie folgt vor, um einem Projekt Beschriftungen hinzuzufügen:

1. Wählen Sie das Projekt auf der Hauptseite von **Datenbeschriftung** aus.
1. Schalten Sie auf der Seite rechts oben den Status **Wird ausgeführt** auf **Angehalten**, um Beschriftungsersteller von ihrer Tätigkeit abzuhalten.
1. Wählen Sie die Registerkarte **Details** aus.
1. Wählen Sie in der Liste auf der linken Seite die Option **Beschriftungsklassen** aus.
1. Wählen Sie im oberen Bereich der Liste **+ Beschriftungen hinzufügen** aus. ![Hinzufügen einer Beschriftung](../articles/machine-learning/media/how-to-create-labeling-projects/add-label.png)
1. Fügen Sie im Formular ihre neue Bezeichnung hinzu. Wählen Sie dann aus, wie das Projekt fortgesetzt werden soll.  Da Sie die verfügbaren Beschriftungen geändert haben, wählen Sie aus, wie mit den bereits beschrifteten Daten verfahren werden soll:
    * „Von vorn beginnen und alle vorhandenen Beschriftungen entfernen.“  Wählen Sie diese Option aus, wenn Sie mit der Beschriftung von vorn beginnen und dabei den neuen vollständigen Beschriftungssatz verwenden möchten. 
    * „Von vorn beginnen und alle vorhandenen Beschriftungen beibehalten.“  Wählen Sie diese Option aus, um alle Daten als nicht beschriftet zu markieren, die vorhandenen Beschriftungen aber als Standardtag für Bilder beizubehalten, die bereits beschriftet wurden.
    * „Fortfahren und alle vorhandenen Beschriftungen beibehalten.“ Wählen Sie diese Option aus, um die bereits beschrifteten Daten unverändert zu lassen und von nun an die neue Beschriftung für noch nicht beschriftete Daten verwenden zu können.
1. Passen Sie Ihre Anweisungsseite nach Bedarf für die neuen Beschriftungen an.
1. Schalten Sie nach dem Hinzufügen aller neuen Beschriftungen oben rechts auf der Seite den Status **Angehalten** auf **Wird ausgeführt**, um das Projekt wieder zu starten. 