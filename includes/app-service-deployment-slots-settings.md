---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/09/2021
ms.author: cephalin
ms.openlocfilehash: 0cc0758c2c4c2c0aca9f1b55ef8ee7c5e5790f31
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "126057193"
---
Wenn Sie die Konfiguration von einem anderen Bereitstellungsslot klonen, kann die geklonte Konfiguration bearbeitet werden. Bei einem Austausch werden einige Konfigurationselemente zusammen mit dem Inhalt überführt (nicht slotspezifisch), während andere Konfigurationselemente nach einem Austausch im gleichen Slot verbleiben (slotspezifisch). Im Anschluss sind die Einstellungen aufgeführt, die sich beim Austauschen der Slots ändert.

**Einstellungen, die ausgetauscht werden**:

* Allgemeine Einstellungen (z. B. Framework-Version, 32/64-Bit-Angabe, WebSockets)
* App-Einstellungen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Verbindungszeichenfolgen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Handlerzuordnungen
* Öffentliche Zertifikate
* WebJobs-Inhalte
* Hybridverbindungen *
* Dienstendpunkte*
* Azure Content Delivery Network*
* Pfadzuordnungen

Für mit einem Sternchen (*) gekennzeichnete Features ist eine Rückgängigmachung des Austauschs geplant. 

**Einstellungen, die nicht ausgetauscht werden**:

* Veröffentlichungsendpunkte
* Benutzerdefinierte Domänennamen
* Nicht öffentliche Zertifikate und TLS/SSL-Einstellungen
* Skalierungseinstellungen
* WebJobs-Planer
* IP-Einschränkungen
* Always On
* Diagnoseeinstellungen
* Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)
* Integration in ein virtuelles Netzwerk
* Verwaltete Identitäten
* Einstellungen, die mit dem Suffix „_EXTENSION_VERSION“ enden

> [!NOTE]
> Damit die genannten Einstellungen ausgetauscht werden können, fügen Sie die App-Einstellung `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` in jedem Slot der App hinzu, und legen Sie ihren Wert auf `0` oder `false` fest. Entweder sind alle diese Einstellungen austauschbar oder überhaupt keine. Sie können nicht einige Einstellungen als austauschbar definieren und andere nicht. Verwaltete Identitäten werden nie ausgetauscht und sind von dieser App-Einstellung zum Außerkraftsetzen nicht betroffen.
>
> Bestimmte App-Einstellungen, die für nicht ausgetauschte Einstellungen gelten, werden ebenfalls nicht ausgetauscht. Da die Diagnoseprotokolleinstellungen beispielsweise nicht ausgetauscht werden, werden verwandte App-Einstellungen wie `WEBSITE_HTTPLOGGING_RETENTION_DAYS` und `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ebenfalls nicht ausgetauscht, auch wenn Sie nicht als Sloteinstellungen angezeigt werden.
>
