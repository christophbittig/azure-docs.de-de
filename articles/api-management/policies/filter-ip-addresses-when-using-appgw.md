---
title: API Management-Beispielrichtlinie – Filtern nach IP-Adresse bei Verwendung von Application Gateway
titleSuffix: Azure API Management
description: 'API Management-Beispielrichtlinie: Demonstriert, wie Sie nach der IP-Adresse einer Anforderung filtern, wenn ein Application Gateway verwendet wird.'
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 0308d8ee4b2b23c578a804c981613338d682a9ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590637"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtern nach der IP-Adresse einer Anforderung bei Verwendung von Application Gateway

Dieser Artikel zeigt ein Beispiel für eine Azure API Management-Richtlinie, das veranschaulicht, wie auf die IP-Adresse einer Anforderung gefiltert wird, wenn über ein Application Gateway oder einen anderen Mittler auf die API Management-Instanz zugegriffen wird. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-reference.md).

## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [API Management-Richtlinien für die Zugriffsbeschränkung](../api-management-access-restriction-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)