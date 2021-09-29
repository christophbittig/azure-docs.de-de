---
title: Azure PowerShell-Skriptbeispiel – Abonnieren eines benutzerdefinierten Themas | Microsoft-Dokumentation
description: In diesem Artikel wird anhand eines Azure PowerShell-Beispielskripts das Abonnieren von Event Grid-Ereignissen für ein benutzerdefiniertes Thema veranschaulicht.
ms.devlang: powershell
ms.topic: sample
ms.date: 09/15/2021
ms.openlocfilehash: 30d328583246c8460e157f706ff68536d74232d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595064"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Abonnieren von Ereignissen für ein benutzerdefiniertes Thema mit PowerShell

Dieses Skript erstellt ein Event Grid-Abonnement für die Ereignisse für ein benutzerdefiniertes Thema.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Für das Vorschaubeispielskript ist das Event Grid-Modul erforderlich. Führen Sie zum Installieren `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` aus.

## <a name="sample-script---stable"></a>Beispielskript: stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Beispielskript: Vorschaumodul

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet den folgenden Befehl, um das Ereignisabonnement zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Erstellen Sie ein Event Grid-Abonnement. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in verwaltete Anwendungen finden Sie in der [Übersicht über verwaltete Azure-Anwendungen](../overview.md).
* Weitere Informationen zu PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/get-started-azureps).
