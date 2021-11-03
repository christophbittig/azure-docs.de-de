---
title: API Management-Beispielrichtlinie – Autorisieren des Zugriffs, basierend auf JWT-Ansprüchen
titleSuffix: Azure API Management
description: Das Beispiel für eine Azure API Management-Richtlinie veranschaulicht, wie Sie den Zugriff auf bestimmte HTTP-Methoden in einer API basierend auf JWT-Ansprüchen autorisieren.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 72f9659bd93cea4cc0adcc638897ba2109fcc046
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065644"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorisieren des Zugriffs basierend auf JWT-Ansprüchen

Dieser Artikel zeigt ein Beispiel für eine Azure API Management-Richtlinie, das veranschaulicht, wie Sie den Zugriff auf bestimmte HTTP-Methoden in einer API basierend auf JWT-Ansprüchen autorisieren. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](index.md).

## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [Transformationsrichtlinien](../api-management-transformation-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)
