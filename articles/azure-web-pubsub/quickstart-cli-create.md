---
title: 'Schnellstart: Erstellen einer Web PubSub-Instanz mit der Azure CLI'
description: In dieser Schnellstartanleitung wird erläutert, wie Sie eine Web PubSub-Instanz mit der Azure CLI erstellen.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 426b77d1e1a79c3a9818e028edcc79bcad5ef2ec
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998075"
---
# <a name="quickstart-create-a-web-pubsub-instance-with-the-azure-cli"></a>Schnellstart: Erstellen einer Web PubSub-Instanz mit der Azure CLI

Die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure) setzt sich aus Befehlen zum Erstellen und Verwalten von Azure-Ressourcen zusammen. Die Azure CLI ist in allen Azure-Diensten verfügbar und soll die schnelle Verwendung von Azure ermöglichen. Der Fokus liegt dabei auf der Automatisierung. In dieser Schnellstartanleitung werden die Optionen zum Erstellen einer Azure Web PubSub-Instanz mit der Azure CLI erläutert.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diese Schnellstartanleitung ist mindestens Version 2.22.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Erstellen einer Web PubSub-Instanz

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="try-the-newly-created-instance"></a>Testen der neu erstellten Instanz

> [!div class="nextstepaction"]
> [Testen der neu erstellten Instanz](./quickstart-cli-try.md#play-with-the-instance)

> [!div class="nextstepaction"]
> [Testen der Instanz mit einer Onlinedemo](./quickstart-live-demo.md#try-the-instance-with-an-online-demo)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Clean up resource](includes/cli-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In realen Anwendungen können Sie SDKs in verschiedenen Sprachen verwenden, um Ihre eigene Anwendung zu erstellen. Außerdem werden Funktionserweiterungen zur Verfügung gestellt, mit deren Hilfe Sie problemlos serverlose Anwendungen erstellen können.

[!INCLUDE [next step](includes/include-next-step.md)]
