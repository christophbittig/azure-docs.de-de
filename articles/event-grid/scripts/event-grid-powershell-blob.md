---
title: 'Azure PowerShell: Abonnieren eines Blobspeicherkontos'
description: In diesem Artikel wird anhand eines Azure PowerShell-Beispielskripts das Abonnieren von Event Grid-Ereignissen für ein Blob Storage-Konto veranschaulicht.
ms.devlang: powershell
ms.topic: sample
ms.date: 09/15/2021
ms.openlocfilehash: 72899bf83d7610576d36a1670cd589d68291baf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602876"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Abonnieren von Ereignissen für ein Blobspeicherkonto mit PowerShell

Dieses Skript erstellt ein Event Grid-Abonnement für die Ereignisse für ein Blobspeicherkonto.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Beispielskript: stabil

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet den folgenden Befehl, um das Ereignisabonnement zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Erstellen Sie ein Event Grid-Abonnement. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](../overview.md).
* Weitere Informationen zu PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/get-started-azureps).
