---
title: Live schalten-API – Azure Marketplace
description: Der Live schalten-API initiiert den Prozess, in dem das Angebot zur Auflistung freigeschaltet (live geschaltet) wird.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: ec036878cf9412bcc612917fa7a104db3a2e8dd0
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240307"
---
# <a name="go-live-api"></a>API zum Liveschalten

> [!NOTE]
> Die Cloud-Partnerportal-APIs sind in Partner Center integriert und funktionieren auch weiterhin. Der Übergang führt zu kleineren Änderungen. Beachten Sie die in der [Cloud-Partnerportal-API-Referenz](./cloud-partner-portal-api-overview.md) aufgeführten Änderungen, um sicherzustellen, dass Ihr Code nach dem Übergang zu Partner Center weiterhin funktioniert. CPP-APIs sollten nur für vorhandene Produkte verwendet werden, die bereits vor dem Übergang zu Partner Center integriert wurden. Neue Produkte sollten die Partner Center-APIs für die Übermittlung verwenden.

Diese API startet den Prozess zum Überführen einer App in den Produktionsstatus. Dieser Vorgang dauert üblicherweise ziemlich lange. In diesem Aufruf wird die E-Mail-Benachrichtigungsliste (notification-emails) aus dem Vorgang in der [Veröffentlichen](./cloud-partner-portal-api-publish-offer.md)-API verwendet.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-Parameter
--------------

|  **Name**      |   **Beschreibung**                                                           | **Datentyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Herausgeber-ID für das abzurufende Angebot, z. B. `contoso`       |  String       |
| offerId        | Angebots-ID des abzurufenden Angebots                                   |  String       |
| api-version    | Aktuelle Version der API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Name**       |     **Wert**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Authorization   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Beispiel für Hauptteil

### <a name="response"></a>Antwort

#### <a name="migrated-offers"></a>Migrierte Angebote

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nicht migrierte Angebote

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Antwortheader

|  **Name**             |      **Wert**                                                            |
|  --------             |      ----------                                                           |
| Standort    |  Der relative Pfad zum Abrufen des Status dieses Vorgangs.            |
|  |  |

### <a name="response-status-codes"></a>Antwortstatuscodes

| **Code** |  **Beschreibung**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`: Die Anforderung wurde erfolgreich angenommen. Die Antwort enthält eine Position, um den Vorgangsstatus nachzuverfolgen. |
|  400     | `Bad/Malformed request`: Zusätzliche Fehlerinformationen sind im Antworttext zu finden. |
|  404     |  `Not found` – die angegebene Entität ist nicht vorhanden.                                       |
|  |  |
