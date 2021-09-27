---
title: API Management-Beispielrichtlinie – Hinzufügen von Funktionen zu einem Back-End-Dienst
titleSuffix: Azure API Management
description: 'Azure API Management-Richtlinienbeispiel: Veranschaulicht, wie Sie einem Back-End-Dienst Funktionen hinzufügen. Beispielsweise können Sie in einer API zur Wettervorhersage anstelle von Längen- und Breitengrad einen Ortsnamen akzeptieren.'
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
ms.openlocfilehash: 561912a534d36768416f7343a92cc02b61498826
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618778"
---
# <a name="add-capabilities-to-a-backend-service"></a>Hinzufügen von Funktionen zu einem Back-End-Dienst

Dieser Artikel veranschaulicht mithilfe eines Beispiels für eine Azure API Management-Richtlinie, wie Sie einem Back-End-Dienst Funktionen hinzufügen. Beispielsweise können Sie in einer API zur Wettervorhersage anstelle von Längen- und Breitengrad einen Ortsnamen akzeptieren. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-reference.md).

## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [Transformationsrichtlinien](../api-management-transformation-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)