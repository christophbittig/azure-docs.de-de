---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 62df24c61cf50f57e5f59ebca8eb2d11b2d03b12
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640628"
---
* Die kleinste AzureBastionSubnet-Subnetzgröße, die Sie erstellen können, ist „/27“. Es wird jedoch empfohlen, mindestens die Größe „/26“ zu verwenden, um die Hostskalierung zu ermöglichen. 
   * Weitere Informationen zur Skalierung finden Sie unter [Instanzen und Hostskalierung (Vorschau)](../articles/bastion/configuration-settings.md#instance).
   * Weitere Informationen zu Einstellungen finden Sie unter [Instanzen und Hostskalierung (Vorschau)](../articles/bastion/configuration-settings.md#instance).
* Erstellen Sie **AzureBastionSubnet** ohne Routentabellen oder Delegierungen. 
* Weitere Informationen zum Verwenden von Netzwerksicherheitsgruppen in **AzureBastionSubnet** finden Sie im Artikel [Arbeiten mit Netzwerksicherheitsgruppen](../articles/bastion/bastion-nsg.md).
