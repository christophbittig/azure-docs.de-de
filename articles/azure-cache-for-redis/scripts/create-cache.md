---
title: 'Erstellen einer Azure Cache for Redis-Instanz: Azure CLI'
description: In diesem Azure CLI-Codebeispiel wird gezeigt, wie Sie mithilfe des Befehls „az redis create“ eine Azure Cache for Redis-Instanz erstellen.
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: c811a5a8f334d74e9c605b1a9fc93c11bf477f1d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582763"
---
# <a name="create-an-azure-cache-for-redis"></a>Erstellen einer Azure Cache for Redis-Instanz

In diesem Szenario erfahren Sie, wie Sie eine Azure Cache for Redis-Instanz erstellen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../includes/redis-cli-script-clean-up.md)]


## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe und eine Azure Cache for Redis-Instanz zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az redis create](/cli/azure/redis) | Erstellt eine Azure Cache for Redis-Instanz. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche Azure Cache for Redis-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Cache for Redis](../cli-samples.md).