---
title: Erstellen einer Cognitive Services-Sprachressource
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie eine Cognitive Services-Sprachressource erstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d21f76e73e9ef4581355890b56876ff2e71a954
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095417"
---
## <a name="create-a-cognitive-services-language-resource"></a>Erstellen einer Cognitive Services-Sprachressource

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Ressource erstellen** aus, und navigieren Sie dann zu **KI + Machine Learning** > **Sprache**.
   Oder navigieren Sie zu [Create a Language resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) (Sprachressource erstellen).
1. Geben Sie alle erforderlichen Einstellungen ein:

    |Einstellung|Wert|
    |--|--|
    |Name|Geben Sie einen Namen ein (2–64 Zeichen).|
    |Subscription|Wählen Sie das entsprechende Abonnement aus.|
    |Standort|Wählen Sie einen nahe gelegenen Speicherort aus.|
    |Tarif| Geben Sie **S** als Standardtarif ein.|
    |Ressourcengruppe|Wählen Sie eine verfügbare Ressourcengruppe aus.|

1. Klicken Sie auf **Erstellen**, und warten Sie, bis die Ressource erstellt wurde. Ihr Browser leitet Sie automatisch zur neu erstellten Ressourcenseite weiter.
1. Rufen Sie den konfigurierten `endpoint` und einen API-Schlüssel ab:

    |Registerkarte „Ressource“ im Portal|Einstellung|Wert|
    |--|--|--|
    |**Übersicht**|Endpunkt|Kopieren Sie den Endpunkt. Dieser sieht etwa so aus: `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0`.|
    |**Schlüssel**|API-Schlüssel|Kopieren Sie einen der beiden Schlüssel. Es handelt sich um eine aus 32 alphanumerischen Zeichen bestehende Zeichenfolge ohne Leerzeichen oder Bindestriche: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
