---
title: Azure CLI-Skriptbeispiel – Abonnieren eines Blobspeicherkontos | Microsoft-Dokumentation
description: In diesem Artikel wird anhand eines Beispielskripts für die Azure-Befehlszeilenschnittstelle das Abonnieren von Ereignissen für ein Azure Blob Storage-Konto veranschaulicht.
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8c5baf79209414031073c8ef0505c0ca18252a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584616"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Abonnieren von Ereignissen für ein Blobspeicherkonto mit der Azure CLI

Dieses Skript erstellt ein Event Grid-Abonnement für die Ereignisse für ein Blobspeicherkonto. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet den folgenden Befehl, um das Ereignisabonnement zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Erstellen Sie ein Event Grid-Abonnement. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - extension version | Erstellen Sie ein Event Grid-Abonnement. |

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Abfragen von Abonnements finden Sie unter [Abfragen von Event Grid-Abonnements](../query-event-subscriptions.md).
* Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
