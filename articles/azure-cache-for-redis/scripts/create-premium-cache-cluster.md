---
title: 'Erstellen einer Azure Cache for Redis-Instanz vom Typ „Premium“ mit Clustering: Azure CLI'
description: In diesem Azure CLI-Codebeispiel erfahren Sie, wie Sie eine Azure Cache for Redis-Instanz mit 6 GB im Premium-Tarif mit aktiviertem Clustering und zwei Shards erstellen.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: d992a3550832baf023906c9a68b5b593d755993a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532720"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Erstellen eines Azure Cache for Redis vom Typ „Premium“ mit Clustering

In diesem Szenario erfahren Sie, wie Sie einen 6 GB-Azure Cache for Redis mit Premium-Tarif erstellen. Dabei ist Clustering aktiviert, und es sind zwei Shards vorhanden.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe und einen Azure Cache for Redis mit Premium-Tarif und aktiviertem Clustering zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az redis create](/cli/azure/redis) | Erstellt eine Azure Cache for Redis-Instanz. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche Azure Cache for Redis-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Cache for Redis](../cli-samples.md).