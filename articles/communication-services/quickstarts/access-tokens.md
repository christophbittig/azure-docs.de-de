---
title: 'Schnellstart: Erstellen und Verwalten von Zugriffstoken'
titleSuffix: An Azure Communication Services quickstart
description: Hier erfahren Sie, wie Sie Identitäten und Zugriffstoken mithilfe des Azure Communication Services Identity SDK verwalten.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 4b83fa7aec50d11c6ec4eac0baf2c15b95e29e6c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677283"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Schnellstart: Erstellen und Verwalten von Zugriffstoken

Steigen Sie in Azure Communication Services ein, indem Sie das Communication Services Identity SDK verwenden. Sie ermöglicht es Ihnen, Identitäten zu erstellen und Ihre Zugriffstoken zu verwalten. Die Identität stellt die Entität Ihrer Anwendung in Azure Communication Service (z. B. Benutzer oder Gerät) dar. Zugriffstoken ermöglichen die direkte Authentifizierung von Chat und Calling SDKs bei Azure Communication Services. Es wird empfohlen, Zugriffstoken mit einem serverseitigen Dienst zu erstellen. Mithilfe von Zugriffstoken werden dann die Communication Services SDKs auf Clientgeräten initialisiert.

Sämtliche Preise, die auf Abbildungen in diesem Tutorial zu sehen sind, dienen lediglich Demonstrationszwecken.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

In der Ausgabe der App wird die jeweils abgeschlossene Aktion beschrieben:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](./create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Identitäten verwalten
> * Ausstellen von Zugriffstoken
> * Verwenden des Communication Services Identity SDK


> [!div class="nextstepaction"]
> [Hinzufügen von Sprachanrufen zu Ihrer App](./voice-video-calling/getting-started-with-calling.md)

Das könnte Sie auch interessieren:

 - [Informationen zur Authentifizierung](../concepts/authentication.md)
 - [Hinzufügen von Chatfunktionen zu Ihrer App](./chat/get-started.md)
 - [Informationen zur Client- und Serverarchitektur](../concepts/client-and-server-architecture.md)
