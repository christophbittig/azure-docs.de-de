---
title: Exportieren von Informationen zu Ihrem Azure-Abonnement auf oberster Ebene
description: In diesem Artikel wird beschrieben, wie Sie alle mit Ihrem Konto verknüpften Azure-Abonnement-IDs anzeigen können.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 34b6e7be93cf42ab4d3ef80110b81fee8a403ed7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561419"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportieren und Anzeigen von Informationen zu Ihrem Abonnement auf oberster Ebene
Wenn Sie den Satz der Abonnement-IDs anzeigen müssen, die Ihren Benutzeranmeldeinformationen zugeordnet sind, [laden Sie eine JSON-Datei mit Informationen zu Ihrem Abonnement aus dem Azure-Kontocenter herunter](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Die heruntergeladene JSON-Datei enthält die folgenden Informationen:
- E-Mail: Die Ihrem Konto zugeordnete E-Mail-Adresse.
- PUID: Der eindeutige Bezeichner, der Ihrem Abrechnungskonto zugeordnet ist.
- Abonnement-IDs: Eine Liste der Abonnements, die zu Ihrem Konto gehören, aufgelistet nach Abonnement-ID.

### <a name="subscriptionsjson-sample"></a>JSON-Beispieldatei für ein Abonnement (subscriptions.json)

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
