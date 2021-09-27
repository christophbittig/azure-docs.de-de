---
title: 'Beispiel für eine API Management-Richtlinie: Generieren von Shared Access Signatures'
titleSuffix: Azure API Management
description: Das Beispiel für eine Azure API Management-Richtlinie veranschaulicht, wie Sie Shared Access Signatures mithilfe von Ausdrücken generieren und die Anforderung mithilfe der rewrite-uri-Richtlinie an Azure Storage weiterleiten.
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
ms.openlocfilehash: 80f0260da56db61205122cde05e2e8be24250187
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654383"
---
# <a name="generate-shared-access-signature"></a>Generieren von Shared Access Signatures

Dieser Artikel zeigt ein Beispiel für eine Azure API Management-Richtlinie, das veranschaulicht, wie Sie [Shared Access Signatures](../../storage/common/storage-sas-overview.md) mithilfe von Ausdrücken generieren und die Anforderung mithilfe der rewrite-uri-Richtlinie an Azure Storage weiterleiten. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [Festlegen oder Bearbeiten von Azure API Management-Richtlinien](../set-edit-policies.md) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-reference.md).

## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [Transformationsrichtlinien](../api-management-transformation-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-reference.md)