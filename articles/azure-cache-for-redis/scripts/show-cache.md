---
title: 'Abrufen von Details zu einer Azure Cache for Redis-Instanz: Azure CLI'
description: In diesem Azure CLI-Codebeispiel erfahren Sie, wie die Details einer Azure Cache for Redis-Instanz abgerufen werden, einschließlich ihres Bereitstellungsstatus.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e41b432fe7259fac9769a70a89bb6357c3d550e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535366"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Abrufen von Details zu einer Azure Cache for Redis-Instanz

In diesem Szenario erfahren Sie, wie die Details einer Azure Cache for Redis-Instanz abgerufen werden, einschließlich ihres Bereitstellungsstatus.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um die Details einer Azure Cache for Redis-Instanz abzurufen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az redis show](/cli/azure/redis) | Ruft Details zu einer Azure Cache for Redis-Instanz ab. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche Azure Cache for Redis-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Cache for Redis](../cli-samples.md).