---
title: Skalieren der Medienverarbeitung mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Dieses Tutorial führt Sie durch die Schritte zur Skalierung der Medienverarbeitung mithilfe des Azure-Portals.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1040ffa8bc3af3734a5c37961676b0914f72e991
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835871"
---
# <a name="change-the-reserved-unit-type"></a>Ändern Sie den Typ reservierter Einheiten

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Übersicht

Reservierte Media-Einheiten werden standardmäßig nicht mehr benötigt und von Azure Media Services nicht unterstützt. Aus Kompatibilitätsgründen verfügt das aktuelle Azure-Portal über eine Option, mit der Sie MRUs verwalten und skalieren können. Standardmäßig wird jedoch keine der von Ihnen festgelegten MRU-Konfigurationen verwendet, um die Parallelität oder Leistung der Codierung zu steuern.

> [!IMPORTANT]
> Lesen Sie unbedingt das [Übersichtsthema](media-services-scale-media-processing-overview.md) , um weitere Informationen zum Skalieren der Medienverarbeitung zu erhalten.

## <a name="scale-media-processing"></a>Skalieren der Medienverarbeitung
>[!NOTE]
>Die Auswahl von MRUs hat keinen Einfluss auf die Parallelität oder Leistung in Azure Media Services V3. 

Führen Sie folgende Schritte aus, um den Typ reservierter Einheiten und die Anzahl reservierter Einheiten zu ändern:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie im Fenster **Einstellungen** die Option **Reservierte Einheiten für Medien** aus.
   
    Verwenden Sie zum Ändern der Anzahl reservierter Einheiten für den ausgewählten Typ den Schieberegler unter **Reservierte Einheiten für Medien** am oberen Bildschirmrand.
   
    Zum Ändern von **RESERVIERTER EINHEITSTYP** klicken Sie auf die Leiste **Geschwindigkeit der reservierten Einheiten für die Verarbeitung**. Wählen Sie dann den benötigten Tarif aus: S1, S2 oder S3.
   
3. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.
   
    Die neuen reservierten Einheiten werden zugewiesen, wenn Sie auf SPEICHERN klicken.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
