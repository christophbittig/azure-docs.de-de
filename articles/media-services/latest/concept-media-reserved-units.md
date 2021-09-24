---
title: 'Reservierte Einheiten für Medien: Azure'
description: Mit reservierten Einheiten für Medien können Sie den Medienprozess skalieren und die Geschwindigkeit Ihrer Medienverarbeitungstasks ermitteln.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3fc68505712bc5cae0defd216b30a3c5913c3c77
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866436"
---
# <a name="media-reserved-units"></a>Reservierte Einheiten für Medien

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Reservierte Einheiten für Medien (Media Reserved Units, MRUs) wurden vormals in Azure Media Services v2 eingesetzt, um die Parallelität und Leistung der Codierung zu steuern. Sie müssen keine MRUs mehr verwalten oder Kontingenterhöhungen für ein Media Services-Konto anfordern, da das System basierend auf der Last automatisch hoch- und herunterskaliert wird. Sie werden außerdem feststellen, dass die erzielte Leistung im Vergleich zur Verwendung von MRUs genauso hoch oder höher ist. 

Wenn Sie über ein Konto verfügen, das mithilfe einer Version vor der API 2020-05-01 erstellt wurde, haben Sie weiterhin Zugriff auf APIs zum Verwalten von MRUs. Allerdings wird keine der von Ihnen festgelegten MRU-Konfigurationen angewandt, um die Parallelität oder Leistung bei der Codierung zu steuern. Wenn die Option zum Verwalten von reservierten Einheiten für Medien im Azure-Portal nicht angezeigt wird, bedeutet dies, dass Sie über ein Konto verfügen, das mit einer API ab Version 2020-05-01 erstellt wurde. 

## <a name="billing"></a>Abrechnung

Während zuvor Kosten für reservierte Einheiten für Medien anfielen, werden ab dem 17. April 2021 keine Gebühren mehr für Konten in Rechnung gestellt, die über eine Konfiguration für reservierte Einheiten für Medien verfügen. Weitere Informationen zur Abrechnung von Codierungsaufträgen finden Sie unter [Codieren von Video- und Audiodaten mit Media Services](encoding-concept.md).

Für Konten, die in der Version **2020-05-01** der API (d. h. der Version v3) oder über das Azure-Portal erstellt werden, sind Skalierungseinheiten und reservierte Einheiten für Medien nicht mehr erforderlich. Die Skalierung wird jetzt automatisch vom Dienst selbst intern vorgenommen. Reservierte Einheiten für Medien werden für kein Azure Media Services-Konto mehr benötigt oder unterstützt. Weitere Informationen finden Sie unter [Reservierte Einheiten für die Codierung (Legacy)](concept-media-reserved-units.md).

## <a name="see-also"></a>Weitere Informationen

* [Migrieren von Media Services V2 zu V3](migrate-v-2-v-3-migration-introduction.md)
* [Skalieren von reservierten Einheiten für Medien mit der CLI](media-reserved-units-cli-how-to.md)
