---
title: 'Schnellstart: Herstellen einer Verbindung mit der Azure Web PubSub-Instanz und Testen der Instanz'
description: Schnellstartanleitung, die gezeigt, wie Sie die Instanz in der Azure CLI testen
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 99045ab4675527f4f0a821025bf6f870d63bb296
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998062"
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
