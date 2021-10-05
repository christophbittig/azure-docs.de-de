---
title: Datenresidenz
description: Datenresidenz und Informationen zu Azure Arc-fähigen Servern.
ms.topic: reference
ms.date: 08/05/2021
ms.custom: references_regions
ms.openlocfilehash: 36aa97dc55879e61caf2b7c19aac21cae4091433
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832184"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc-fähige Server: Datenresidenz

In diesem Artikel wird das Konzept der Datenresidenz und dessen Anwendung auf Azure Arc-fähigen Servern erläutert.

Azure Arc-fähige Server sind in den **USA, in Europa, dem Vereinigten Königreich, Australien und der Asien-Pazifik-Region** **[verfügbar](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>Datenresidenz

Azure Arc-fähige Server speichern Konfigurationseinstellungen (d. h. Eigenschaftswerte) der [Azure-VM-Erweiterung](manage-vm-extensions.md), die für die Erweiterung angegeben werden müssen, bevor sie auf dem verbundenen Computer aktiviert werden können. Wenn Sie z. B. die Log Analytics-VM-Erweiterung aktivieren, werden Sie nach der **Arbeitsbereichs-ID** und dem **Primärschlüssel** von Log Analytics gefragt.

Metadateninformationen zum verbundenen Computer werden ebenfalls gesammelt. Dies betrifft insbesondere:

* Betriebssystemname, -typ und -version
* Computername
* Vollqualifizierter Domänenname (FQDN) des Computers
* Version des Connected Machine-Agents
* Vollqualifizierter Domänenname (FQDN) für Active Directory und DNS
* UUID (BIOS-ID)
* Connected Machine-Agent-Takt
* Version des Connected Machine-Agents
* Öffentlicher Schlüssel für verwaltete Identität
* Richtlinienkonformitätsstatus und Details (bei Verwendung von Richtlinien für Gastkonfigurationen)

Bei Azure Arc-fähigen Servern können Sie die Region angeben, in der Ihre Daten gespeichert werden sollen. Microsoft kann die Daten in Hinblick auf die Datenresilienz in andere Regionen replizieren; Microsoft repliziert aber keine Daten außerhalb des geografischen Gebiets oder verschiebt Daten in diesen Raum. Diese Daten werden in der Region gespeichert, in der die Azure Arc-Computerressource konfiguriert wird. Wenn der Computer z. B. mit Azure Arc in der Region „USA, Osten“ registriert ist, werden diese Daten in der Region „USA“ gespeichert.

> [!NOTE] 
> Für „Asien, Südosten“ werden Ihre Daten nicht außerhalb dieser Region repliziert. 

Weitere Informationen zur Unterstützung der regionalen Resilienz und Compliance finden Sie unter [Azure-Geographie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwerfen für [Azure-Resilienz](/azure/architecture/reliability/architect).
