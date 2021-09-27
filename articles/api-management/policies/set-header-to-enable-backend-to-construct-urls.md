---
title: Beispiel für eine Azure API Management-Richtlinie – Hinzufügen eines Forwarded-Headers | Microsoft-Dokumentation
description: Das Beispiel für eine Azure API Management-Richtlinie veranschaulicht, wie Sie einen Forwarded-Header in die eingehende Anforderung einfügen, damit die Back-End-API die richtigen URLs erstellen kann.
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
ms.openlocfilehash: efa64b4847ae2effe20090e3fcb962c0c9e56d8f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647041"
---
# <a name="add-a-forwarded-header"></a>Hinzufügen eines Forwarded-Headers

Dieser Artikel zeigt ein Beispiel für eine Azure API Management-Richtlinie, das veranschaulicht, wie Sie einen Forwarded-Header in die eingehende Anforderung einfügen, damit die Back-End-API die richtigen URLs erstellen kann. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-reference.md).

## <a name="code"></a>Code

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [Transformationsrichtlinien](../api-management-transformation-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)