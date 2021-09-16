---
title: Was ist die Funktion zur automatischen Registrierung in privaten Azure DNS-Zonen?
description: Übersicht über die Funktion zur automatischen Registrierung in privaten Azure DNS-Zonen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: 0854412a83f941e43e79ebcde2f70ae62dabdf15
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965160"
---
# <a name="what-is-the-auto-registration-feature-in-azure-dns-private-zones"></a>Was ist die Funktion zur automatischen Registrierung in privaten Azure DNS-Zonen?

Die Funktion zur automatischen Registrierung in privaten Azure DNS-Zonen verwaltet DNS-Einträge für virtuelle Computer, die in einem virtuellen Netzwerk bereitgestellt werden. Wenn Sie [ein virtuelles Netzwerk mit einer privaten DNS-Zone verknüpfen](./private-dns-virtual-network-links.md) und diese Einstellung aktiviert ist, wird für jeden virtuellen Computer, der im virtuellen Netzwerk bereitgestellt wird, ein DNS-Eintrag erstellt. 

Für jeden virtuellen Computer werden ein A-Eintrag und ein PTR-Eintrag erstellt. DNS-Einträge für neu bereitgestellte virtuelle Computer werden ebenfalls automatisch in der verknüpften privaten DNS-Zone erstellt. Wird ein virtueller Computer gelöscht, werden alle zugeordneten DNS-Einträge ebenfalls aus der privaten DNS-Zone gelöscht.

Zum Aktivieren der automatischen Registrierung wählen Sie beim Erstellen der VNET-Verknüpfung das Kontrollkästchen „Automatische Registrierung aktivieren“ aus.

:::image type="content" source="./media/privatedns-concepts/enable-autoregistration.png" alt-text="Screenshot der Option „Automatische Registrierung aktivieren“ auf der Seite „VNET-Verknüpfung hinzufügen“":::

## <a name="restrictions"></a>Beschränkungen

* Die automatische Registrierung funktioniert nur für virtuelle Computer. Für alle anderen Ressourcen, z. B. interne Lastenausgleichsmodule, können Sie DNS-Einträge manuell in der privaten DNS-Zone erstellen, die mit dem virtuellen Netzwerk verknüpft ist.
* DNS-Einträge werden nur für die primäre NIC des virtuellen Computers automatisch erstellt. Wenn Ihre virtuellen Computer über mehr als eine Netzwerkkarte verfügen, können Sie die DNS-Einträge für andere Netzwerkschnittstellen manuell erstellen.
* DNS-Einträge werden nur dann automatisch erstellt, wenn die primäre NIC des virtuellen Computers DHCP verwendet. Wenn Sie statische IP-Adressen verwenden, z. B. eine Konfiguration mit [mehreren IP-Adressen in Azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config), werden bei der automatischen Registrierung keine Einträge für diesen virtuellen Computer erstellt.
* Die automatische Registrierung für IPv6 (AAAA-Einträge) wird nicht unterstützt.
* Die automatische Registrierung für private DNS-Zonen ist auf ein einzelnes VNET beschränkt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie über [Azure PowerShell](./private-dns-getstarted-powershell.md) oder die [Azure-Befehlszeilenschnittstelle](./private-dns-getstarted-cli.md) eine private Zone in Azure DNS erstellen.

* Informieren Sie sich über einige gängige [Szenarien zu privaten Zonen](./private-dns-scenarios.md), die mit privaten Zonen in Azure DNS realisiert werden können.

* Allgemeine Fragen und Antworten zu privaten Zonen in Azure DNS, einschließlich des spezifischen Verhaltens für bestimmte Vorgänge, finden Sie unter [Häufig gestellte Fragen zu privatem Azure DNS](./dns-faq-private.yml).
