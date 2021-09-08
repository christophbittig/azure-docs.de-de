---
title: Unterstützte Kategorien für die Erkennung benannter Entitäten
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die unterstützten Entitätskategorien in der Textanalyse-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 08/11/2021
ms.author: aahi
ms.openlocfilehash: 9d63fc9f90408fd60331f77079fdd8f15ca4aad6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339423"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Unterstützte Entitätskategorien in der Textanalyse-API v3.

Ermitteln Sie anhand der Informationen in diesem Artikel die Entitätskategorien, die von der [Erkennung benannter Entitäten](how-tos/text-analytics-how-to-entity-linking.md) (Named Entity Recognition, NER) zurückgegeben werden können. Die NER (Erkennung benannter Entitäten) führt ein Vorhersagemodell aus, um benannte Entitäten aus einem Eingabedokument zu identifizieren und zu kategorisieren.

Darüber hinaus ist NER v3.1 verfügbar, die eine Funktion zum Erkennen von personenbezogenen Informationen (`PII`) und von Gesundheitsinformationen (`PHI`) beinhaltet. Klicken Sie außerdem auf die Registerkarte **Integrität**, um eine Liste der unterstützten Kategorien in Text Analytics for Health anzuzeigen. 

Im [Migrationsleitfaden](migration-guide.md?tabs=named-entity-recognition) finden Sie eine Liste der von Version 2.1 zurückgegebenen Typen.

## <a name="entity-categories"></a>Entitätskategorien

#### <a name="general"></a>[Allgemein](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Gesundheit](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der Erkennung benannter Entitäten in der Textanalyse](how-tos/text-analytics-how-to-entity-linking.md)
