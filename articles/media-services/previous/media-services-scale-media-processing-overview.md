---
title: Reservierte Einheiten für Medien – Übersicht | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die Skalierung der Medienverarbeitung mithilfe von Azure Media Services.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 19454d90b55ab92c92d2e9132198f918f38c6e2c
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835382"
---
# <a name="media-reserved-units"></a>Reservierte Einheiten für Medien

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Reservierte Einheiten für Medien (Media Reserved Units, MRUs) wurden vormals eingesetzt, um die Parallelität und Leistung der Codierung zu steuern. MRUs werden nur für die folgenden Legacy-Medienprozessoren genutzt, die in Kürze als veraltet eingestuft werden. Informationen zur Deaktivierung dieser Legacy-Prozessoren finden Sie unter [Legacy-Komponenten von Azure Media Services](legacy-components.md):

* Media Encoder Premium Workflow
* Media Indexer V1 und V2

Für alle anderen Medienprozessoren müssen Sie keine MRUs mehr verwalten oder Kontingenterhöhungen für ein Media Services-Konto anfordern, da das System basierend auf der Last automatisch hoch- und herunterskaliert wird. Sie werden außerdem feststellen, dass die erzielte Leistung im Vergleich zur Verwendung von MRUs genauso hoch oder höher ist.

## <a name="billing"></a>Abrechnung

Während zuvor Kosten für reservierte Einheiten für Medien anfielen, werden ab dem 17. April 2021 keine Gebühren mehr für Konten in Rechnung gestellt, die über eine Konfiguration für reservierte Einheiten für Medien verfügen.

## <a name="scaling-mrus"></a>Skalieren von MRUs

Aus Kompatibilitätsgründen können Sie weiterhin das Azure-Portal oder die folgenden APIs verwenden, um MRUs zu verwalten und zu skalieren:

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Standardmäßig wird jedoch keine der von Ihnen festgelegten MRU-Konfigurationen verwendet, um die Parallelität oder Leistung der Codierung zu steuern. Die einzige Ausnahme liegt vor, wenn Sie für die Codierung mit einem der folgenden Legacy-Medienprozessoren arbeiten: Media Encoder Premium Workflow oder Media Indexer V1.  
