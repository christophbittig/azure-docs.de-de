---
title: Regionen, in denen Azure Video Analyzer for Media (früher Video Indexer) verfügbar ist
titleSuffix: Azure Video Analyzer for Media
description: In diesem Artikel werden Azure-Regionen behandelt, in denen Azure Video Analyzer for Media (früher Video Indexer) verfügbar ist.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 1db75ea3ba67d4e8f0551495274ec3fff38e29b4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119180"
---
# <a name="azure-regions-in-which-video-analyzer-for-media-exists"></a>Azure-Regionen, in denen Video Analyzer for Media vorhanden ist

Die APIs von Azure Video Analyzer for Media (früher Video Indexer) enthalten einen **location**-Parameter, den Sie auf die Azure-Region festlegen müssen, an die der Aufruf weitergeleitet werden soll. Dabei muss es sich um eine [Azure-Region handeln, in der Video Analyzer for Media verfügbar ist](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Standorte

Dem Parameter `location` muss der Codename der Azure-Region als Wert zugewiesen werden. Wenn Sie Video Analyzer for Media im Vorschaumodus verwenden, müssen Sie `"trial"` als Wert verwenden. `trial` ist der Standardwert für den Parameter `location`. Andernfalls können Sie das Azure-Portal verwenden oder einen [Azure CLI-Befehl](/cli/azure) ausführen, um den Codenamen der Azure-Region abzurufen, in der sich Ihr Konto befindet und an die Ihr Aufruf weitergeleitet werden soll.

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich auf der [Azure Video Analyzer for Media](https://www.videoindexer.ai/) Website an.
1. Klicken Sie rechts oben auf der Seite auf **Benutzerkonten**.
1. Suchen Sie in der oberen rechten Ecke nach dem Standort Ihres Kontos.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Location":::
    
###  <a name="cli-command"></a>CLI-Befehl

```azurecli-interactive
az account list-locations
```

Nach dem Ausführen der oben gezeigten Zeile erhalten Sie eine Liste mit allen Azure-Regionen. Navigieren Sie zu der Azure-Region, die den *displayName* aufweist, nach dem Sie suchen, und verwenden ihren Wert *name* für den Parameter **location**.

Verwenden Sie für die Azure-Region „USA, Westen 2“ (unten angezeigt) beispielsweise „westus2“ als Wert für den Parameter **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Nächste Schritte

- [Anpassen des Sprachmodells mit APIs](customize-language-model-with-api.md)
- [Anpassen des Markenmodells mit APIs](customize-brands-model-with-api.md)
- [Anpassen des Personenmodells mit APIs](customize-person-model-with-api.md)
