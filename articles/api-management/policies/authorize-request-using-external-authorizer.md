---
title: API Management-Beispielrichtlinie – Autorisieren von Anforderungen mit einem externen Autorisierer
titleSuffix: Azure API Management
description: Das Beispiel für eine Azure API Management-Richtlinie veranschaulicht, wie Sie Anforderungen mithilfe eines externen Autorisierers autorisieren, der eine benutzerdefinierte oder ältere Authentifizierungs-/Autorisierungslogik kapselt.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: danlep
ms.openlocfilehash: 4f91eb6da5cea64eac4b324391a09e879e7ebb27
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670232"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorisieren von Anforderungen mit einem externen Autorisierer

Dieser Artikel veranschaulicht anhand eines Beispiels für eine Azure API Management-Richtlinie, wie Sie den API-Zugriff mithilfe eines externen Autorisierers schützen, der eine benutzerdefinierte Authentifizierungs-/Autorisierungslogik kapselt. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-reference.md).

## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [API Management-Richtlinien für die Zugriffsbeschränkung](../api-management-access-restriction-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)