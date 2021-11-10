---
title: Microservices mit Azure Container Apps (Vorschau)
description: Erstellen eines Microservice in Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39d095f28c9f642815e5463975b9836e228d9c3a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028741"
---
# <a name="microservices-with-azure-containers-apps-preview"></a>Microservices mit Azure Container Apps (Vorschau)

[Microservicearchitekturen](https://azure.microsoft.com/solutions/microservice-applications/#overview) ermöglichen ihnen das unabhängige Entwickeln, Upgraden, Versionieren und Skalieren von Kernfunktionalitätsbereichen in einem Gesamtsystem. Azure Container Apps bietet mit den folgenden Features die Grundlage für die Bereitstellung von Microservices:

- Unabhängige [Skalierung](scale-app.md), [Versionierung](application-lifecycle-management.md) und [Upgrades](application-lifecycle-management.md)
- [Dienstermittlung](connect-apps.md)
- Native [Dapr-Integration](microservices-dapr.md)

:::image type="content" source="media/microservices/azure-container-services-microservices.png" alt-text="Container-Apps werden als Microservices bereitgestellt.":::

Eine [Umgebung](environment.md) in Container Apps bietet eine Sicherheitsgrenze um eine Gruppe von Container-Apps. Eine einzelne Container-App stellt in der Regel einen Microservice dar, der aus Pods besteht, die aus einem oder mehreren [Containern](containers.md) bestehen.

## <a name="dapr-integration"></a>Dapr-Integration

Bei der Implementierung eines Systems, das aus Microservices besteht, sind Funktionsaufrufe über das Netzwerk verteilt. Zur Unterstützung der verteilten Natur von Microservices müssen Sie Fehler, Wiederholungen und Timeouts berücksichtigen. Während Container Apps die Bausteine für die Ausführung von Microservices zur Verfügung stellt, bietet die Verwendung von [Dapr](https://docs.dapr.io/concepts/overview/) ein noch umfangreicheres Modell zur Programmierung von Microservices. Dapr umfasst Features wie Einblick, Pub/Sub und Service-to-Service-Aufrufe mit gegenseitiger TLS, Wiederholungen und mehr.

Weitere Informationen zur Verwendung von Dapr finden Sie unter [Erstellen von Microservices mit Dapr](microservices-dapr.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Skalieren](scale-app.md)
