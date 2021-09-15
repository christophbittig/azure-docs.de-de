---
title: 'Skalieren reservierter Einheiten für Medien: CLI'
description: In diesem Thema wird gezeigt, wie Sie die CLI zum Skalieren der Medienverarbeitung mit Azure Media Services verwenden.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: b8dc092104f3371263719adbec16305afb87113c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867381"
---
# <a name="how-to-scale-media-reserved-units-legacy"></a>Skalieren reservierter Einheiten für Medien (veraltet)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel erfahren Sie, wie Sie reservierte Einheiten für Medien (Media Reserved Units, MRUs) für eine schnellere Codierung skalieren.

> [!WARNING]
> Dieser Befehl funktioniert nicht mehr für Media Services-Konten, die mit der API-Version 2020-05-01 oder höher erstellt werden. Für diese Konten sind keine reservierten Einheiten für Medien mehr erforderlich, da das System je nach Auslastung automatisch zentral hoch- und herunterskaliert wird. Wenn die Option zum Verwalten von reservierten Einheiten für Medien im Azure-Portal nicht angezeigt wird, bedeutet dies, dass Sie ein Konto verwenden, das mit einer API ab Version 2020-05-01 erstellt wurde.
> Mit diesem Artikel soll lediglich der Legacyprozess der Verwendung von MRUs dokumentiert werden.

## <a name="prerequisites"></a>Voraussetzungen

[Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md)

Grundkenntnisse über [reservierte Einheiten für Medien](concept-media-reserved-units.md)

## <a name="scale-media-reserved-units-with-cli"></a>Skalieren von reservierten Einheiten für Medien mit der CLI

Führen Sie den Befehl `mru` aus.

Mit dem folgenden Befehl [az ams account mru](/cli/azure/ams/account/mru) werden reservierte Einheiten für Medien im Konto „amsaccount“ festgelegt, indem die Parameter **count** und **type** verwendet werden.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Abrechnung

 Während zuvor Gebühren für reservierte Einheiten für Medien angefallen sind, werden ab dem 17. April 2021 keine Gebühren mehr für Konten abgerechnet, die über eine Konfiguration für reservierte Einheiten für Medien verfügen.

## <a name="see-also"></a>Weitere Informationen

* [Migrieren von Media Services V2 zu V3](migrate-v-2-v-3-migration-introduction.md)
