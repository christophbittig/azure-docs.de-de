---
author: baanders
description: Include für Abfrage-API-Latenzhinweis
ms.service: digital-twins
ms.topic: include
ms.date: 09/21/2021
ms.author: baanders
ms.openlocfilehash: 2e538b6f0197f408be649bd76cb4a15fbe22b4bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557656"
---
>[!NOTE]
>Nachdem Sie eine Änderung an den Daten in Ihrem Graphen vorgenommen haben, kann eine Latenz von bis zu 10 Sekunden auftreten, bevor die Änderungen in Abfragen berücksichtigt werden. 
>
>Die [DigitalTwins-API](../articles/digital-twins/concepts-apis-sdks.md#overview-data-plane-apis) spiegelt Änderungen sofort wider. Wenn Sie also eine sofortige Antwort benötigen, verwenden Sie eine API-Anforderung ([DigitalTwins GetById](/rest/api/digital-twins/dataplane/twins/digitaltwins_getbyid)) oder einen SDK-Aufruf ([GetDigitalTwin](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.getdigitaltwin?view=azure-dotnet&preserve-view=true)), um Zwillingsdaten zu erhalten, anstatt eine Abfrage durchzuführen.