---
title: include file
description: include file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ab3994f44ed5a77ba228d9338d40599012597a95
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908293"
---
## <a name="message-headers"></a>Nachrichtenheader
Dies sind die Eigenschaften, die Sie in den Nachrichtenheadern empfangen:

| Eigenschaftenname | BESCHREIBUNG |
| ------------- | ----------- | 
| aeg-subscription-name | Name des Ereignisabonnements. |
| aeg-delivery-count | Anzahl von Versuchen, die für das Ereignis durchgeführt wurden |
| aeg-event-type | <p>Der Typ des Ereignisses</p><p>Es kann sich um einen der folgenden Werte handeln:</p><ul><li>SubscriptionValidation</li><li>Benachrichtigung</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>Die Metadatenversion des Ereignisses<p> Bei einem **Event Grid-Ereignisschema** stellt diese Eigenschaft die Metadatenversion und bei einem **Cloudereignisschema** die **Spezifikationsversion** dar. </p>|
| aeg-data-version | <p>Die Datenversion des Ereignisses</p><p>Bei einem **Event Grid-Ereignisschema** stellt diese Eigenschaft die Datenversion dar. Bei einem **Cloudereignisschema** kommt sie nicht zur Anwendung</p> |
| aeg-output-event-id | ID des Event Grid-Ereignisses |


