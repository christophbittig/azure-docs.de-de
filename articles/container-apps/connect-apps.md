---
title: Verbinden von Anwendungen in Azure Container Apps (Vorschauversion)
description: Es wird beschrieben, wie Sie mehrere Anwendungen bereitstellen, die in Azure Container Apps miteinander kommunizieren.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10fe890ecd4bb2bc89cea71d1a70f7df9eb38e1e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094858"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Verbinden von Anwendungen in Azure Container Apps (Vorschauversion)

In Azure Container Apps wird jede Container-App über einen Domänennamen verfügbar gemacht, wenn [Datenverkehr in eingehender Richtung](ingress.md) aktiviert ist. Eingangsendpunkte können entweder öffentlich verfügbar gemacht werden, oder nur intern und für andere Container-Apps in derselben [Umgebung](environment.md).

Wenn Sie den Domänennamen einer Container-App kennen, können Sie den Standort in Ihrem Anwendungscode aufrufen, um mehrere Container-Apps miteinander zu verbinden.

## <a name="location"></a>Standort

Der Standort einer Container-App umfasst die Werte für die Umgebung, den Namen und die Region. Für den vollqualifizierten Domänennamen, der über die Domäne `azurecontainerapps.io` der obersten Ebene verfügbar ist, wird Folgendes verwendet:

- Name der Container-App
- Eindeutiger Bezeichner der Umgebung
- Regionsname

Im folgenden Diagramm ist dargestellt, wie diese Werte verwendet werden, um den vollqualifizierten Domänennamen einer Container-App zu erstellen.

:::image type="content" source="media/connect-apps/azure-container-apps-location.png" alt-text="Azure Container Apps: Vollqualifizierter Domänenname der Container-App":::

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

## <a name="dapr-location"></a>Dapr-Speicherort

Für die Entwicklung von Microservices müssen Sie häufig Muster implementieren, die für verteilte Architekturen häufig genutzt werden. Mit Dapr können Sie Microservices per gegenseitigem TLS schützen, bei Fehlern Wiederholungsversuche auslösen und die verteilte Ablaufverfolgung nutzen, wenn Azure Application Insights aktiviert ist.

Ein Microservice, für den Dapr verwendet wird, ist über das folgende URL-Muster verfügbar:

:::image type="content" source="media/connect-apps/azure-container-apps-location-dapr.png" alt-text="Azure Container Apps: Speicherort der Container-App mit Dapr":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte](get-started.md)
