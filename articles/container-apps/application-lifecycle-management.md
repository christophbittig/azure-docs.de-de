---
title: Verwaltung des Anwendungslebenszyklus in Azure Container Apps (Vorschauversion)
description: Erfahren Sie mehr über den vollständigen Anwendungslebenszyklus in der Vorschauversion von Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: f731ceae2059c5b0e48f1f1428b60a988ee12ce0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027183"
---
# <a name="application-lifecycle-management-in-azure-container-apps-preview"></a>Verwaltung des Anwendungslebenszyklus in Azure Container Apps (Vorschauversion)

Der Anwendungslebenszyklus von Azure Container Apps dreht sich um [Revisionen](revisions.md).

Wenn Sie eine Container-App bereitstellen, wird die erste Revision automatisch erstellt. [Weitere Revisionen werden erstellt](revisions.md), wenn sich die [Container](containers.md) ändern oder wenn Anpassungen an dem Abschnitt `template` der Konfiguration vorgenommen werden.

Eine Container-App durch durchläuft drei Phasen: Bereitstellung, Update und Deaktivierung.

## <a name="deployment"></a>Bereitstellung

Wenn Sie eine Container-App bereitstellen, wird die erste Revision automatisch erstellt.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deployment.png" alt-text="Azure Container Apps: Bereitstellungsphase":::

## <a name="update"></a>Aktualisieren

Wenn eine Container-App mit einer [Revisionsumfangsänderung](revisions.md#revision-scope-changes)aktualisiert wird, wird eine neue Revision erstellt. Sie können auswählen, [ob neue alte Revisionen automatisch deaktiviert oder verfügbar bleiben sollen](revisions.md).

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-update.png" alt-text="Azure Container Apps: Updatephase":::

## <a name="deactivate"></a>Deaktivieren

Sobald eine Revision nicht mehr benötigt wird, können Sie eine Revision mit der Option deaktivieren, diese später erneut zu aktivieren. Während der Deaktivierung wird der Container [heruntergefahren](#shutdown).

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deactivate.png" alt-text="Azure Container Apps: Deaktivierungsphase":::

## <a name="shutdown"></a>Shutdown

Die Container werden in den folgenden Situationen heruntergefahren:

- Beim Abskalieren einer Container-App
- Beim Löschen einer Container-App
- Beim Deaktivieren einer Revision

Wenn das Herunterfahren eingeleitet wird, sendet der Containerhost eine [SIGTERM-Nachricht](https://wikipedia.org/wiki/Signal_(IPC)) an Ihren Container. Der im Container implementierte Code kann auf diese Meldung auf Betriebssystemebene reagieren, um die Beendigung zu behandeln.

Wenn Ihre Anwendung nicht auf die `SIGTERM` Nachricht reagiert, beendet [SIGKILL](https://wikipedia.org/wiki/Signal_(IPC)) Ihren Container.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Arbeiten mit Revisionen](revisions.md)
