---
title: Durchführen eines Upgrades für eine öffentliche IP-Adresse (Azure-Portal)
description: In diesem Artikel wird erläutert, wie Sie mithilfe des Azure-Portals ein Upgrade für eine öffentliche IP-Adresse der Basic-SKU durchführen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: a40806f6328c513fe6a835ca3a81b46ad962b86d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224947"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-portal"></a>Durchführen eines Upgrades für eine öffentliche IP-Adresse mithilfe des Azure-Portals

Öffentliche Azure-IP-Adressen werden mit einer SKU (Basic oder Standard) erstellt. Durch die SKU wird ihre Funktionalität festgelegt, darunter Zuordnungsmethode, Featureunterstützung und Ressourcen, die ihnen zugeordnet werden können. 

In diesem Artikel wird erläutert, wie Sie im Azure-Portal ein Upgrade für eine statische öffentliche IP-Adresse der Basic-SKU auf die Standard-SKU durchführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Eine statische öffentliche IP-Adresse der Basic-SKU in Ihrem Abonnement. Weitere Informationen finden Sie unter [Erstellen einer öffentlichen IP-Adresse im Azure-Portal](./create-public-ip-portal.md#create-a-basic-sku-public-ip-address).

## <a name="upgrade-public-ip-address"></a>Durchführen eines Upgrades für eine öffentliche IP-Adresse

In diesem Abschnitt melden Sie sich beim Azure-Portal an und führen ein Upgrade für Ihre statische öffentliche IP-Adresse der Basic-SKU auf die Standard-SKU durch.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

4. Wählen Sie unter **Öffentliche IP-Adressen** die Option **myBasicPublicIP** oder die IP-Adresse aus, für die Sie ein Upgrade durchführen möchten.

5. Klicken Sie unter **myBasicPublicIP** oben im Abschnitt mit der Übersicht auf das Upgradebanner.

    :::image type="content" source="./media/public-ip-upgrade-portal/upgrade-ip-portal.png" alt-text="Durchführen eines Upgrades für eine IP-Adresse der Basic-SKU im Azure-Portal" border="true":::

    > [!NOTE]
    > Die öffentliche IP-Adresse der Basic-SKU, für die ein Upgrade ausgeführt werden soll, muss den statischen Zuordnungstyp aufweisen. Wenn Sie versuchen, ein Upgrade für eine dynamisch zugeordnete IP-Adresse durchzuführen, erhalten Sie eine Warnung, dass das Upgrade der IP-Adresse nicht möglich ist.

6.  Aktivieren Sie das Kontrollkästchen **Bestätigen**. Wählen Sie **Upgrade** aus.

    > [!WARNING]
    > Ein Upgrade einer öffentlichen IP-Adresse der Basic-SKU auf die Standard-SKU kann nicht rückgängig gemacht werden. Auch nach dem Upgrade von der Basic-SKU auf die Standard-SKU verfügen die öffentlichen IP-Adressen nicht über garantierte [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="verify-upgrade"></a>Bestätigen des Upgrades

In diesem Abschnitt überprüfen Sie, ob die öffentliche IP-Adresse jetzt der Standard-SKU entspricht.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

4. Wählen Sie unter **Öffentliche IP-Adressen** die Option **myBasicPublicIP** oder die IP-Adresse aus, für die Sie ein Upgrade ausgeführt haben.

5. Stellen Sie sicher, dass die SKU im Abschnitt **Übersicht** als **Standard** aufgeführt wird.

    :::image type="content" source="./media/public-ip-upgrade-portal/verify-upgrade-ip.png" alt-text="Überprüfen, ob die öffentliche IP-Adresse die Standard-SKU aufweist" border="true":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel führen Sie ein Upgrade für eine öffentliche IP-Adresse von der Basic-SKU auf die Standard-SKU durch.

Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter:

- [Öffentliche IP-Adressen in Azure](public-ip-addresses.md)
- [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](./create-public-ip-portal.md)