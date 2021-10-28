---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 413328496d35346ef6b7219bbed40755160ce9eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287488"
---
* **Eine [öffentliche IPv4-Adresse](../articles/virtual-network/ip-services/public-ip-addresses.md#standard) mit Standard-SKU**. Die öffentliche IP-Adressressource ist erforderlich, wenn Sie das virtuelle Netzwerk für den externen oder internen Zugriff einrichten. Bei einem internen virtuellen Netzwerk wird die öffentliche IP-Adresse nur für Verwaltungsvorgänge verwendet. Erfahren Sie mehr über [IP-Adressen von API Management](../articles/api-management/api-management-howto-ip-addresses.md).

  * Die IP-Adresse muss sich in derselben Region und im gleichen Abonnement wie die API Management-Instanz und das virtuelle Netzwerk befinden.

  * Der Wert der IP-Adresse wird als virtuelle öffentliche IPv4-Adresse der API Management Instanz in dieser Region zugewiesen. 

  * Wenn Sie von einem externen zu einem internen virtuellen Netzwerk wechseln (oder umgekehrt), Subnetze im Netzwerk ändern oder Verfügbarkeitszonen für die API Management Instanz aktualisieren, müssen Sie eine andere öffentliche IP-Adresse konfigurieren.