---
title: Azure Container Apps-Umgebungen (Vorschau)
description: Erfahren Sie, wie Umgebungen in Azure Container Apps verwaltet werden.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 07d54583b884c071708910daea45f3dcb94d14f3
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578309"
---
# <a name="azure-container-apps-preview-environments"></a>Azure Container Apps-Umgebungen (Vorschauversion)

Einzelne Container-Apps werden in einer einzelnen Container Apps-Umgebung bereitgestellt, die als sichere Grenze für Gruppen von Container-Apps fungiert. Container-Apps, die in derselben Umgebung bereitgestellt wurden, werden auch im gleichen virtuellen Netzwerk bereitgestellt und schreiben Protokolle in denselben Log Analytics-Arbeitsbereich.

:::image type="content" source="media/environments/azure-container-apps-environments.png" alt-text="Azure Container Apps-Umgebungen.":::

Gründe für die Bereitstellung von Container-Apps in derselben Umgebung ergeben sich in Situationen, in denen Folgendes erforderlich ist:

- Verwalten zugehöriger Dienste
- Bereitstellen verschiedener Anwendungen im gleichen virtuellen Netzwerk
- Möglichkeit zur Kommunikation der Anwendungen untereinander mithilfe von Dapr
- Nutzung der gleichen Dapr-Konfiguration durch mehrere Anwendungen
- Verwenden desselben Log Analytics-Arbeitsbereichs durch mehrere Anwendungen

Zu den Gründen für die Bereitstellung von Container-Apps in verschiedenen Umgebungen gehören Situationen, in denen Sie Folgendes sicherstellen möchten:

- Zwei Anwendungen verwenden nie dieselben Computeressourcen
- Zwei Anwendungen können nicht über Dapr miteinander kommunizieren

## <a name="logs"></a>Protokolle

Einstellungen, die für die API-Ressource der Azure Container-Apps-Umgebung relevant sind

| Eigenschaft | BESCHREIBUNG |
|---|---|
| `properties.appLogsConfiguration` | Wird zum Konfigurieren des Log Analytics-Arbeitsbereichs verwendet, in dem Protokolle für alle Apps in der Umgebung veröffentlicht werden. |
| `properties.containerAppsConfiguration.daprAIInstrumentationKey` | App Insights Instrumentierungsschlüssel, der Dapr für die Ablaufverfolgung zur Verfügung gestellt wird |

## <a name="billing"></a>Abrechnung

Für die Abrechnung sind nur die einzelnen Container-Apps und deren Ressourcennutzung relevant. Der Container Apps-Umgebung sind keine Grundgebühren zugeordnet.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten des Verhaltens der automatischen Skalierung](scale-app.md)