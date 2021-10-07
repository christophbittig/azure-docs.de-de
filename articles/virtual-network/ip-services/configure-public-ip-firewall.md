---
title: Verwalten einer öffentlichen IP-Adresse mit Azure Firewall
titleSuffix: Azure Virtual Network
description: Hier finden Sie Informationen zur Verwendung einer öffentlichen IP-Adresse mit Azure Firewall sowie zum Ändern der Konfiguration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: f520b992e018b41a8adf99b87202975a4a2edd38
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368199"
---
# <a name="manage-a-public-ip-address-with-azure-firewall"></a>Verwalten einer öffentlichen IP-Adresse mit Azure Firewall

Azure Firewall ist ein cloudbasierter Netzwerksicherheitsdienst zum Schutz Ihrer Azure Virtual Network-Ressourcen. Für Azure Firewall muss mindestens eine öffentliche statische IP-Adresse konfiguriert werden. Diese IP-Adresse oder Gruppe von IP-Adressen wird als externer Verbindungspunkt für die Firewall verwendet. Azure Firewall unterstützt öffentliche IP-Adressen der Standard-SKU. Öffentliche IP-Adressen und Präfixe für öffentliche IP-Adressen der Basic-SKU werden nicht unterstützt. 

In diesem Artikel erfahren Sie, wie Sie eine Azure Firewall-Instanz mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. Sie werden die IP-Konfiguration der Firewall ändern. Abschließend fügen Sie der Firewall eine IP-Konfiguration hinzu.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Drei öffentliche IP-Adressen der Standard-SKU in Ihrem Abonnement. Die IP-Adresse darf keiner Ressource zugeordnet sein. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](../../virtual-network/create-public-ip-portal.md).
    - Benennen Sie die neuen öffentlichen IP-Adressen für die Beispiele in diesem Artikel mit **myStandardPublicIP-1**, **myStandardPublicIP-2** und **myStandardPublicIP-3**.

## <a name="create-azure-firewall-existing-public-ip"></a>Erstellen einer Azure Firewall-Instanz mit einer vorhandenen öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie eine Azure Firewall-Instanz. Sie wählen die in den Voraussetzungen erstellte IP-Adresse als öffentliche IP-Adresse für die Firewall aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie in das Suchfeld am oberen Rand des Portals **Firewall** ein.

3. Wählen Sie in den Suchergebnissen **Firewalls** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Firewall erstellen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupFW** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myFirewall** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Verfügbarkeitszone | Übernehmen Sie den Standardwert **Keine**. |
    | Firewallebene  | Übernehmen Sie den Standardwert **Standard**. |
    | Firewallverwaltung | Behalten Sie den Standard **Firewallrichtlinie zum Verwalten dieser Firewall verwenden** bei.|
    | Firewallrichtlinie | Wählen Sie **Neue hinzufügen** aus. </br> Geben Sie **myFirewallPolicy** unter **Richtlinienname** ein. </br> Wählen Sie unter **Region** die Option **USA, Westen 2** aus. </br> Wählen Sie **Ja**. |
    | Name des virtuellen Netzwerks | Geben Sie **myVNet** ein. |
    | Adressraum | Geben Sie **10.0.0.0/16** ein. |
    | Subnetzadressraum | Geben Sie **10.0.0.0/26** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **myStandardPublicIP-1** oder Ihre öffentliche IP-Adresse aus. |
    | Tunnelerzwingung | Übernehmen Sie den Standardwert **Deaktiviert**. |
    
 
6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Dies ist eine einfache Bereitstellung von Azure Firewall. Informationen zur erweiterten Konfiguration und Einrichtung finden Sie im [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall und einer Richtlinie über das Azure-Portal](../../firewall/tutorial-firewall-deploy-portal-policy.md).
>
> Weitere Informationen zu Azure Firewall finden Sie unter [Was ist Azure Firewall?](../../firewall/overview.md).

## <a name="change-public-ip-address"></a>Ändern einer öffentlichen IP-Adresse

In diesem Abschnitt ändern Sie die öffentliche IP-Adresse, die der Firewall zugeordnet ist. Einer Firewall muss in der Konfiguration mindestens eine öffentliche IP-Adresse zugeordnet werden. 

1. Geben Sie in das Suchfeld am oberen Rand des Portals **Firewall** ein.

2. Wählen Sie in den Suchergebnissen **Firewalls** aus.

3. Wählen Sie unter **Firewalls** die Option **myFirewall** aus.

4. Wählen Sie unter **myFirewall** unter **Einstellungen** die Option **Konfiguration der öffentlichen IP-Adresse** aus.

5. Wählen Sie unter **Konfiguration der öffentlichen IP-Adresse** die Option **myStandardPublicIP-1** oder Ihre IP-Adresse aus.

6. Wählen Sie unter **Öffentliche IP-Adresse** von **Konfiguration der öffentlichen IP-Adresse bearbeiten** die Option **myStandardPublicIP-2** aus.

7. Wählen Sie **Speichern** aus.

## <a name="add-public-ip-configuration"></a>Hinzufügen der Konfiguration einer öffentlichen IP-Adresse

In diesem Abschnitt fügen Sie der Azure Firewall-Instanz die Konfiguration einer öffentlichen IP-Adresse hinzu. Weitere Informationen zu mehreren IP-Adressen finden Sie unter [Mehrere öffentliche IP-Adressen](../../firewall/features.md#multiple-public-ip-addresses).  

1. Geben Sie in das Suchfeld am oberen Rand des Portals **Firewall** ein.

2. Wählen Sie in den Suchergebnissen **Firewalls** aus.

3. Wählen Sie unter **Firewalls** die Option **myFirewall** aus.

4. Wählen Sie unter **myFirewall** unter **Einstellungen** die Option **Konfiguration der öffentlichen IP-Adresse** aus.

5. Wählen Sie **+ Konfiguration einer öffentlichen IP-Adresse hinzufügen** aus.

6. Geben Sie **myNewPublicIPconfig** unter **Name** ein.

7. Wählen Sie unter **Öffentliche IP-Adresse** die Option **myStandardPublicIP-3** aus.

8. Wählen Sie **Hinzufügen**.

## <a name="more-information"></a>Weitere Informationen

* Eine Azure Firewall-Instanz kann in einen Lastenausgleich der Standard-SKU integriert werden, um Back-End-Poolressourcen zu schützen.  Wenn Sie die Firewall einem öffentlichen Lastenausgleich zuordnen, konfigurieren Sie den eingehenden Datenverkehr so, dass er an die öffentliche IP-Adresse der Firewall geleitet wird. Konfigurieren Sie den ausgehenden Datenverkehr über eine benutzerdefinierte Route zur öffentlichen IP-Adresse der Firewall.  Weitere Informationen und Anleitungen zur Einrichtung finden Sie unter [Integrieren von Azure Firewall mit Azure Load Balancer Standard](../../firewall/integrate-lb.md). 

* Eine Azure Firewall-Instanz kann auch einem NAT-Gateway zugeordnet werden, um die Erweiterbarkeit der Quellnetzwerkadressen-Übersetzung (Source Network Address Translation, SNAT) zu erweitern. Ein NAT-Gateway vermeidet Konfigurationen, um Datenverkehr von einer großen Anzahl öffentlicher IP-Adressen zu ermöglichen, die der Firewall zugeordnet sind. Bei dieser Konfiguration verwendet der ganze eingehende Datenverkehr die öffentliche IP-Adresse oder die Adressen des NAT-Gateways. Der ausgehende Datenverkehr läuft über die öffentliche IP-Adresse bzw. die Adressen der Azure Firewall-Instanz.  Weitere Informationen finden Sie unter [Skalierung der SNAT-Ports mit Azure NAT Gateway](../../firewall/integrate-with-nat-gateway.md).

## <a name="caveats"></a>Vorbehalte

* Azure Firewall verwendet den Lastenausgleich der Standard-SKU. Andere Protokolle als TCP und UDP in Netzwerkfilterregeln werden für die öffentliche IP-Adresse der Firewall nicht für SNAT unterstützt. 
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine Azure Firewall-Instanz erstellen und eine vorhandene öffentliche IP-Adresse verwenden. Sie haben die öffentliche IP-Adresse der Standard-IP-Konfiguration geändert. Schließlich haben Sie der Firewall eine öffentliche IP-Konfiguration hinzugefügt.

- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](../../virtual-network/public-ip-addresses.md).
- Weitere Informationen zu Azure Firewall finden Sie unter [Was ist Azure Firewall?](../../firewall/overview.md).
