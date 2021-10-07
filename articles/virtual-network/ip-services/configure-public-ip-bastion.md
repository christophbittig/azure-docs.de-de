---
title: Verwalten einer öffentlichen IP-Adresse mit Azure Bastion
titleSuffix: Azure Virtual Network
description: Hier finden Sie Informationen zur Verwendung einer öffentlichen IP-Adresse mit Azure Bastion sowie zum Ändern der Konfiguration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 22fa5e1843cdff45a5bea9fb61393a0579278964
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368218"
---
# <a name="manage-a-public-ip-address-with-azure-bastion"></a>Verwalten einer öffentlichen IP-Adresse mit Azure Bastion

Öffentliche IP-Adressen sind in zwei SKUs verfügbar. „Standard“ und „Basic“. Die Features der IP-Adresse richten sich nach der Wahl der SKU. Die SKU bestimmt die Ressourcen, denen die IP-Adresse zugeordnet werden kann.

Azure Bastion wird bereitgestellt, um eine sichere Verwaltungskonnektivität für virtuelle Computer in einem virtuellen Netzwerk zu gewährleisten. Mit dem Azure Bastion-Dienst können Sie sichere und nahtlose RDP- oder SSH-Verbindungen mit den virtuellen Computern in Ihrem virtuellen Netzwerk herstellen. Azure Bastion ermöglicht Verbindungen ohne Verfügbarmachung einer öffentlichen IP-Adresse auf dem virtuellen Computer. Verbindungen werden direkt über das Azure-Portal hergestellt – ganz ohne zusätzlichen Client/Agent oder zusätzliche Software. Azure Bastion unterstützt öffentliche IP-Adressen der Standard-SKU.

Ein Azure Bastion-Host benötigt eine öffentliche IP-Adresse für die Konfiguration.

In diesem Artikel erfahren Sie, wie Sie einen Azure Bastion-Host mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. Die öffentliche IP-Adresse kann nach der Erstellung nicht mehr geändert werden.  Von Azure Bastion werden keine Präfixe für öffentliche IP-Adressen unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Eine öffentliche IP-Adresse der Standard-SKU in Ihrem Abonnement. Die IP-Adresse darf keinen Ressourcen zugeordnet sein. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](../../virtual-network/create-public-ip-portal.md).
    - Benennen Sie die neue öffentliche IP-Adresse für die Beispiele in diesem Artikel mit **myStandardPublicIP**.

## <a name="create-azure-bastion-using-existing-ip"></a>Erstellen von Azure Bastion mit einer vorhandenen IP-Adresse

In diesem Abschnitt wird ein Azure Bastion-Host erstellt. Sie wählen die in den Voraussetzungen erstellte IP-Adresse als öffentliche IP-Adresse für den Bastionhost aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Bastion** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Bastions** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Bastion-Instanz erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert | 
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |  |
    | Name | Geben Sie **myBastionHost** aus. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | **Konfigurieren eines virtuellen Netzwerks** |   |
    | Virtuelles Netzwerk | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myVNet** ein. </br> Lassen Sie den Standardadressraum **10.4.0.0/16** unverändert. </br> Lassen Sie das Standardsubnetz **10.4.0.0/24** unverändert. </br> Geben Sie im Textfeld unter dem Subnetz **Standard** den Namen **AzureBastionSubnet** ein. </br> Geben Sie für den Adressbereich den Wert **10.4.1.0/27** ein. </br> Klicken Sie auf **OK**. |
    | Subnet | Wählen Sie **AzureBastionSubnet** aus. |
    | **Öffentliche IP-Adresse** |   |
    | Öffentliche IP-Adresse | Wählen Sie **Vorhandenes verwenden** aus. |
    | Öffentliche IP-Adresse wählen | Wählen Sie **myStandardPublicIP** aus. |

6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](../../bastion/bastion-overview.md).

## <a name="change-or-remove-public-ip-address"></a>Ändern oder Entfernen öffentlicher IP-Adressen

Das Ändern der öffentlichen IP-Adresse nach der Erstellung wird von Azure Bastion nicht unterstützt.

## <a name="more-information"></a>Weitere Informationen

* Bei der Verbindungsherstellung über Azure Bastion wird keine separate öffentliche IP-Adresse auf dem virtuellen Computer benötigt. Datenverkehr wird zuerst an die öffentliche IP-Adresse von Bastion weitergeleitet. Anschließend werden RDP- oder SSH-Verbindungen von Bastion an die private IP-Adresse weitergeleitet, die dem virtuellen Computer zugeordnet ist. 

## <a name="caveats"></a>Vorbehalte

* Öffentliche IPv6-Adressen werden derzeit für Azure Bastion nicht unterstützt.  

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine Azure Bastion-Instanz erstellen und eine vorhandene öffentliche IP-Adresse verwenden. 

- Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](../../bastion/bastion-overview.md).
- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](../../virtual-network/public-ip-addresses.md).
