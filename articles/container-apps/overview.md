---
title: Übersicht über Azure Container Apps (Vorschauversion)
description: Erfahren Sie mehr über gängige Szenarien und Verwendungen von Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: overview
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 720d243796781ea2be2f01796c1892e64132f58a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028608"
---
# <a name="azure-container-apps-preview-overview"></a>Übersicht über Azure Container Apps (Vorschauversion)

Mit Azure Container Apps können Sie Microservices und Containeranwendungen auf einer serverlosen Plattform ausführen. Zu den gängigen Verwendungsmöglichkeiten von Azure Container Apps gehören die folgenden:

- Bereitstellen von API-Endpunkten
- Hosten von Hintergrundverarbeitungsanwendungen
- Verarbeiten der ereignisgesteuerten Verarbeitung
- Ausführen von Microservices

Anwendungen, die auf Azure Container Apps basieren, können basierend auf den folgenden Merkmalen dynamisch skaliert werden:

- HTTP-Datenverkehr
- Ereignisgesteuerte Verarbeitung
- CPU- oder Arbeitsspeicherauslastung
- Jede [von KEDA unterstützte Skalierungsfunktion](https://keda.sh/docs/scalers/)

:::image type="content" source="media/overview/azure-container-apps-example-scenarios.png" alt-text="Beispielszenarien für Azure Container Apps":::

Azure Container Apps ermöglicht die Ausführung von Anwendungscode, der in einem beliebigen Container gepackt ist, und ist unabhängig von der Runtime oder dem Programmiermodell. Mit Container Apps genießen Sie die Vorteile von Containern und müssen sich nicht mehr um die Verwaltung von Cloudinfrastrukturen und komplexen Containerorchestratoren kümmern.

Mit Azure Container Apps haben Sie folgende Möglichkeiten:

- [**Mehrere Containerrevisionen ausführen**](application-lifecycle-management.md) und den Anwendungslebenszyklus der Container-App verwalten

- [**Autoskalierung**](scale-app.md) für Ihre Apps basierend auf einem von KEDA unterstützten Skalierungstrigger. Die meisten Anwendungen können auf Null<sup>1</sup> skaliert werden.

- [**HTTPS-Eingang aktivieren**](ingress.md), ohne eine andere Azure-Infrastruktur verwalten zu müssen

- [**Datenverkehr aufteilen**](revisions.md) auf mehrere Versionen einer Anwendung für Blau/Grün-Bereitstellungen und A/B-Testszenarien

- [**Interne Eingangs- und Diensterfassung verwenden**](connect-apps.md) für sichere, rein interne Endpunkte mit integrierter DNS-basierter Dienstermittlung

- [**Microservices mit Dapr erstellen**](microservices.md) und auf deren umfangreichen API-Satz zugreifen

- [**Container von einer beliebigen Registrierung ausführen**](containers.md) (öffentlich oder privat), einschließlich Docker Hub und Azure Container Registry (ACR)

- [**Azure CLI-Erweiterung oder ARM-Vorlagen verwenden**](get-started.md), um Ihre Anwendungen zu verwalten

- [**Geheimnisse auf sichere Weise direkt in Ihrer Anwendung verwalten**](secure-app.md)

- [**Anwendungsprotokolle mithilfe von Azure Log Analytics anzeigen**](monitor.md)

<sup>1</sup> Anwendungen, die [nach CPU- oder Arbeitsspeicherauslastung skaliert werden](scale-app.md), können nicht auf Null skaliert werden.

### <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen Ihrer ersten Container-App](get-started.md)
