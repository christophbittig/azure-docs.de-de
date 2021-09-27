---
title: 'Beispiel für eine Azure API Management-Richtlinie: Verwenden von OAuth2 für die Autorisierung zwischen Gateway und Back-End'
titleSuffix: Azure API Management
description: Das Beispiel für eine Azure API Management-Richtlinie veranschaulicht, wie Sie OAuth2 für die Autorisierung zwischen dem Gateway und einem Back-End verwenden. Dieses zeigt, wie Sie ein Zugriffstoken aus AAD abrufen und an das Back-End weiterleiten.
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
ms.openlocfilehash: 9cdb4a81de70cc020500178215578c2638aac276
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648751"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Verwenden von OAuth2 für die Autorisierung zwischen dem Gateway und einem Back-End

Dieser Artikel zeigt ein Beispiel für eine Azure API Management-Richtlinie, das veranschaulicht, wie Sie OAuth2 für die Autorisierung zwischen dem Gateway und einem Back-End verwenden. Dieses zeigt, wie Sie ein Zugriffstoken aus AAD abrufen und an das Back-End weiterleiten. 

Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-reference.md).

Das folgende Skript verwendet Eigenschaften, die in {{property}} angezeigt werden. Weitere Informationen über Eigenschaften und deren Verwendung in API Management-Richtlinien finden Sie in [diesem](../api-management-howto-properties.md) Thema.
 
## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [Transformationsrichtlinien](../api-management-transformation-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)