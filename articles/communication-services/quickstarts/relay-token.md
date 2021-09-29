---
title: Schnellstart – Zugreifen auf TURN-Relays
description: Erfahren Sie, wie Sie mithilfe von Azure Communication Services ein STUN/TURN-Token abrufen
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 7e449fa88a080fd9568436c7806767bac52f1861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677148"
---
# <a name="quickstart-access-turn-relays"></a>Schnellstart: Zugreifen auf TURN-Relays

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Netzwerk-Relaytoken für den Zugriff auf TURN-Server von Azure Communication Services abrufen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free)
- Eine aktive Azure Communication Services-Ressource. Informationen zum Erstellen einer Communication Services-Ressource finden Sie bei Bedarf [hier](./create-communication-resource.md).

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Communication Services-Ressource bereinigen und entfernen möchten, können Sie die Ressource oder Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](./create-communication-resource.md#clean-up-resources).
