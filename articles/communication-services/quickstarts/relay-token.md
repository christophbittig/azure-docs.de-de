---
title: Schnellstart – Zugreifen auf TURN-Relays
description: Erfahren Sie, wie Sie mithilfe von Azure Communication Services ein STUN/TURN-Token abrufen
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 09/28/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 0e813fe3098b71478a88f1a8cf52c523efaaa82f
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893217"
---
# <a name="quickstart-access-turn-relays"></a>Schnellstart: Zugreifen auf TURN-Relays

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

::: zone pivot="programming-language-python"
[!INCLUDE [Get a network relay token with Python](./includes/relay-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get a network relay token with Java](./includes/relay-token-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Communication Services-Ressource bereinigen und entfernen möchten, können Sie die Ressource oder Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](./create-communication-resource.md#clean-up-resources).

