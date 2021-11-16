---
title: Grundlegende Azure Web PubSub-Konzepte für Hubs, Gruppen und Verbindungen
description: Erhalten Sie ein besseres Verständnis der Begriffe, die in Azure Web PubSub verwendet werden.
author: lianwei
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: dc05e81228149fbeae647af81f97bc1e3a320c1f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997702"
---
# <a name="azure-web-pubsub-basic-concepts"></a>Grundlegende Azure Web PubSub-Konzepte

Der Azure Web PubSub-Dienst ermöglicht die Erstellung von Webanwendungen für Echtzeit-Messaging. Die Clients stellen über das [normale WebSocket-Protokoll](https://datatracker.ietf.org/doc/html/rfc6455) eine Verbindung mit dem Dienst her, und der Dienst macht [REST-APIs](/rest/api/webpubsub) und SDKs verfügbar, um diese Clients verwalten zu können.

## <a name="terms"></a>Begriffe

Nachstehend sind einige wichtige Begriffe aufgeführt, die vom Dienst verwendet werden:

[!INCLUDE [Terms](includes/terms.md)]

## <a name="workflow"></a>Workflow

Hier sehen Sie einen typischen Workflow, der den Dienst verwendet:

![Darstellung des Web PubSub-Dienstworkflows.](./media/concept-service-internals/workflow.png)

Das obige Workflowdiagramm zeigt Folgendes:

1. Ein *Client* stellt über den WebSocket-Transport eine Verbindung mit dem `/client`-Endpunkt des Diensts her. Der Dienst leitet sämtliche WebSocket-Frames an den konfigurierten Upstream(server) weiter. Über die WebSocket-Verbindung kann eine Verbindung mit einem beliebigen benutzerdefinierten Unterprotokoll hergestellt werden, das der Server verarbeiten soll. Alternativ kann eine Verbindung mit dem vom Dienst unterstützten Unterprotokoll `json.webpubsub.azure.v1` hergestellt werden, wodurch die Clients Nachrichten direkt veröffentlichen oder abonnieren können. Einzelheiten finden Sie unter [Clientprotokoll](concept-service-internals.md#client_protocol).

2. Der Dienst ruft den Server über das **CloudEvents-HTTP-Protokoll** für unterschiedliche Clientereignisse auf. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) ist eine standardisierte und protokollunabhängige Definition der Struktur und Metadatenbeschreibung von Ereignissen, die von der Cloud Native Computing Foundation (CNCF) gehostet werden. Einzelheiten finden Sie unter [Serverprotokoll](concept-service-internals.md#server_protocol).

3. Der Server kann den Dienst mithilfe der REST-API aufrufen, um Nachrichten an Clients zu senden oder die verbundenen Clients zu verwalten. Einzelheiten finden Sie unter [Serverprotokoll](concept-service-internals.md#server_protocol).