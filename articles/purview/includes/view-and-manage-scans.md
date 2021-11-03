---
author: whhender
ms.author: whhender
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 6/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ea867a13772b4a0d10d21d3ca732320b404d9c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010883"
---
### <a name="view-your-scans-and-scan-runs"></a>Betrachten Sie Ihre Überprüfungen und Überprüfungsausführungen

Gehen Sie zum Anzeigen vorhandener Überprüfungen wie folgt vor:

1. Wechseln Sie zu [Purview Studio](https://web.purview.azure.com/resource/). Wählen Sie im linken Bereich die Registerkarte **Data Map** aus.

1. Wählen Sie die gewünschte Datenquelle aus. Daraufhin wird unter **Letzte Überprüfungen** eine Liste mit vorhandenen Überprüfungen für diese Datenquelle angezeigt. Alternativ dazu können Sie alle Überprüfungen auf der Registerkarte **Überprüfungen** anzeigen.

1. Wählen Sie die Überprüfung mit den Ergebnissen aus, die Sie anzeigen möchten.

1. Auf dieser Seite werden alle vorherigen Überprüfungsausführungen sowie der Status und die Metriken der einzelnen Überprüfungsausführungen angezeigt. Außerdem sehen Sie hier, ob es sich um eine geplante oder um eine manuelle Überprüfung gehandelt hat, auf wie viele Ressourcen Klassifizierungen angewendet wurden, wie viele Ressourcen insgesamt ermittelt wurden, wann die Überprüfung gestartet und beendet wurde und wie lange die Überprüfung insgesamt gedauert hat.

### <a name="manage-your-scans---edit-delete-or-cancel"></a>Verwalten Ihrer Überprüfungen: Bearbeiten, Löschen oder Abbrechen

Gehen Sie zum Verwalten oder Löschen einer Überprüfung wie folgt vor:

1. Wechseln Sie zu [Purview Studio](https://web.purview.azure.com/resource/). Wählen Sie im linken Bereich die Registerkarte **Data Map** aus.

1. Wählen Sie die gewünschte Datenquelle aus. Daraufhin wird unter **Letzte Überprüfungen** eine Liste mit vorhandenen Überprüfungen für diese Datenquelle angezeigt. Alternativ dazu können Sie alle Überprüfungen auf der Registerkarte **Überprüfungen** anzeigen.

1. Wählen Sie die Überprüfung aus, die Sie verwalten möchten. Wenn Sie die Überprüfung bearbeiten möchten, klicken Sie auf **Überprüfung bearbeiten**.

1. Wenn Sie eine laufende Überprüfung abbrechen möchten, klicken Sie auf **Überprüfungsausführung abbrechen**.

1. Wenn Sie Ihre Überprüfung löschen möchten, klicken Sie auf **Überprüfung löschen**.

> [!NOTE]
> * Beim Löschen Ihrer Überprüfung werden nicht die Katalogressourcen aus den vorherigen Überprüfungen gelöscht.
> * Die Ressource wird nicht mehr mit Schemaänderungen aktualisiert, wenn die Quelltabelle geändert wurde und Sie die Quelltabelle nach dem Bearbeiten der Beschreibung auf der Registerkarte „Schema“ von Purview erneut überprüfen.
