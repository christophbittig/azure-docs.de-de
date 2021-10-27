---
title: Verwalten einer Warteschlange im Auftragsrouter
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Azure Communication Services SDKs zum Verwalten des Verhaltens einer Warteschlange
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f7aaf45cea9ced8721d9b13da4d8ab34b249f2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075036"
---
# <a name="manage-a-queue"></a>Verwalten einer Warteschlange

In diesem Leitfaden werden die Schritte zum Erstellen und Verwalten einer Auftragsrouter-Warteschlange beschrieben.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Optional: Absolvieren Sie die Schnellstartanleitung für [die ersten Schritte mit dem Auftragsrouter](../../quickstarts/router/get-started-router.md).

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Verwenden Sie zum Erstellen einer einfachen Warteschlange im Auftragsrouter das SDK, um die **Warteschlangen-ID,** den **Namen** und eine **Verteilungsrichtlinien-ID** anzugeben. Die Verteilungsrichtlinie muss im Voraus erstellt werden, da der Auftragsrouter seine Existenz beim Erstellen der Warteschlange überprüft. Im folgenden Beispiel wird eine Verteilungsrichtlinie erstellt, um zu steuern, wie Auftragsangebote für Worker generiert werden.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);

var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```
## <a name="update-a-queue"></a>Aktualisieren einer Warteschlange

Das Auftragsrouter-SDK erstellt eine neue Warteschlange oder aktualisiert eine vorhandene Warteschlange, wenn die Methode `SetQueue` oder `SetQueueAsync` aufgerufen wird.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```

## <a name="delete-a-queue"></a>Löschen einer Warteschlange

Um eine Warteschlange mit dem Auftragsrouter-SDK zu löschen, rufen Sie die Methode `DeleteQueue` oder `DeleteQueueAsync` auf, die die **Warteschlangen-ID** übergibt.

```csharp
var result = await client.DeleteQueueAsync("XBOX_DEFAULT_QUEUE");
```

> [!NOTE]
> Um eine Warteschlange zu löschen, müssen Sie sicherstellen, dass ihr keine aktiven Aufträge zugewiesen sind. Stellen Sie außerdem sicher, dass keine Verweise auf die Warteschlange in Klassifizierungsrichtlinien oder Regeln vorhanden sind, die einen Ausdruck verwenden, um die Warteschlange anhand einer ID mithilfe eines Zeichenfolgenwerts auszuwählen.