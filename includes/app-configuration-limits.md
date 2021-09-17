---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 3b04007ef7329231da7de31ec465de2c1585c7e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752837"
---
| Resource | Begrenzung | Comment |
| --- | --- | ---|
| Konfigurationsspeicher für Free-Tarif | 1 Speicher pro Abonnement |
| Konfigurationsspeicher für Standard-Tarif | Unbegrenzte Speicher pro Abonnement | 
| Konfigurationsspeicheranforderungen für Free-Tarif | 1\.000 Anforderungen pro Tag  | Nach Ausschöpfung des Kontingents wird bis zum Tagesende für alle Anforderungen der HTTP-Statuscode 429 zurückgegeben. |
| Konfigurationsspeicheranforderungen für Standard-Tarif | 30.000 pro Stunde  |Nach Ausschöpfung des Kontingents wird bis zum Tagesende für alle Anforderungen der HTTP-Statuscode 429 (zu viele Anforderungen) zurückgegeben.|  
| Speicher für Free-Tarif | 10 MB |
| Speicher für Standard-Tarif | 1 GB |
| Schlüssel und Werte | 10 KB  | Für ein einzelnes Schlüssel-Wert-Element, einschließlich aller Metadaten
