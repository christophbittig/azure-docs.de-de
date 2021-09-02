---
title: Erstellen eines vertrauenswürdigen Benutzerzugriffsdiensts mithilfe von Azure Functions in Azure Communication Services
titleSuffix: An Azure Communication Services tutorial
description: Erfahren Sie, wie Sie mithilfe von Azure Functions einen vertrauenswürdigen Benutzerzugriffsdienst für Communication Services erstellen.
author: ddematheu2
manager: chpalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: a2598d56d74c159c2db00aaa26b9bde04a7d2bcb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258968"
---
# <a name="build-a-trusted-user-access-service-using-azure-functions"></a>Erstellen eines vertrauenswürdigen Benutzerzugriffsdiensts mithilfe von Azure Functions

In diesem Artikel wird beschrieben, wie Sie Azure Functions verwenden, um einen vertrauenswürdigen Benutzerzugriffsdienst zu erstellen.

> [!IMPORTANT]
> Der am Ende dieses Tutorials erstellte Endpunkt ist nicht sicher. Lesen Sie dazu unbedingt die Sicherheitsdetails im Artikel [Azure Functions-Sicherheit.](../../azure-functions/security-concepts.md) Sie müssen den Endpunkt sicher gestalten, um zu gewährleisten, dass Angreifer keine Tokens bereitstellen können.

[!INCLUDE [Trusted Service JavaScript](./includes/trusted-service-js.md)]

## <a name="securing-azure-function"></a>Absichern von Azure Functions

Wenn Sie einen vertrauenswürdigen Dienst für die Bereitstellung von Zugriffstoken für Benutzer einrichten, müssen Sie auch die Sicherheit des Endpunkts berücksichtigen, um zu gewährleisten, dass kein Angreifer Tokens für Ihren Dienst erstellen kann. Azure Functions bietet integrierte Sicherheitsfeatures, mit denen Sie den Endpunkt mithilfe von verschiedenen Authentifizierungsrichtlinien schützen können. Hier erhalten Sie weitere Informationen über die [Sicherheit von Azure Functions](../../azure-functions/security-concepts.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Hier finden Sie weitere Informationen zum [Bereinigen von Azure Communication Service-Ressourcen](../quickstarts/create-communication-resource.md#clean-up-resources) und zum [Bereinigen von Azure Functions-Ressourcen](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen über die Sicherheit von Azure Functions](../../azure-functions/security-concepts.md)

> [!div class="nextstepaction"]
> [Hinzufügen von Sprachanrufen zu Ihrer App](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Das könnte Sie auch interessieren:

- [Hinzufügen von Chatfunktionen zu Ihrer App](../quickstarts/chat/get-started.md)
- [Erstellen von Benutzerzugriffstoken](../quickstarts/access-tokens.md)
- [Erfahren Sie mehr über die Client- und Serverarchitektur](../concepts/client-and-server-architecture.md)
- [Informationen zur Authentifizierung](../concepts/authentication.md)