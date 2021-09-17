---
title: 'Schnellstart: Herstellen einer Verbindung mit der Azure Web PubSub-Instanz und Testen der Instanz'
description: Schnellstartanleitung, die gezeigt, wie Sie die Instanz in der Azure CLI testen
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 08/06/2021
ms.openlocfilehash: ac81c3422036abafc5e2d034e06e81e1f35d2e02
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445384"
---
# <a name="quickstart-connect-to-the-azure-web-pubsub-instance-from-cli"></a>Schnellstart: Herstellen einer Verbindung mit der Azure Web PubSub-Instanz über die CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie über die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) eine Verbindung mit der Azure Web PubSub-Instanz herstellen und Nachrichten an die verbundenen Clients veröffentlichen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diese Schnellstartanleitung ist mindestens Version 2.22.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Erstellen einer Web PubSub-Instanz

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="play-with-the-instance"></a>Testen der Instanz

[!INCLUDE [Try the Web PubSub instance](includes/cli-awps-client.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung erhalten Sie grundlegende Informationen dazu, wie Sie eine Verbindung mit dem Web PubSub-Dienst herstellen und Nachrichten auf den verbundenen Clients veröffentlichen.

In realen Anwendungen können Sie SDKs in verschiedenen Sprachen verwenden, um Ihre eigene Anwendung zu erstellen. Außerdem werden Funktionserweiterungen zur Verfügung gestellt, mit deren Hilfe Sie problemlos serverlose Anwendungen erstellen können.

[!INCLUDE [next step](includes/include-next-step.md)]
