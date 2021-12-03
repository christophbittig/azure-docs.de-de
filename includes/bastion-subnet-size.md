---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 8a56b564d9126197b76d6e96cc56bb51eca980eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521269"
---
* Die kleinste AzureBastionSubnet-Subnetzgröße, die Sie erstellen können, ist /26. Es wird empfohlen, mindestens die Größe /26 zu verwenden, um die Hostskalierung zu ermöglichen. 
   * Weitere Informationen zur Skalierung finden Sie unter [Instanzen und Hostskalierung (Vorschau)](../articles/bastion/configuration-settings.md#instance).
   * Weitere Informationen zu Einstellungen finden Sie unter [Instanzen und Hostskalierung (Vorschau)](../articles/bastion/configuration-settings.md#instance).
* Erstellen Sie **AzureBastionSubnet** ohne Routentabellen oder Delegierungen. 
* Weitere Informationen zum Verwenden von Netzwerksicherheitsgruppen in **AzureBastionSubnet** finden Sie im Artikel [Arbeiten mit Netzwerksicherheitsgruppen](../articles/bastion/bastion-nsg.md).
