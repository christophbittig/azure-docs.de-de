---
title: Verwalten von virtuellen Netzwerken – Azure Database for MySQL – Flexible Server
description: Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL – Flexible Server über das Azure-Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 076dc23568c2cc6570da8f7ee1e614a225568dc5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472673"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Erstellen und Verwalten von virtuellen Netzwerken für die Azure Datenbank für MySQL-flexible Server über das Azure-Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Azure Database for MySQL Flexible Server unterstützt zwei Arten von sich gegenseitig ausschließenden Netzwerkverbindungsmethoden, mit denen eine Verbindung mit Ihrem flexiblen Server hergestellt werden kann. Die zwei Optionen sind:

- Öffentlicher Zugriff (zugelassene IP-Adressen)
- Privater Zugriff (VNET-Integration)

In diesem Artikel wird die Erstellung von MySQL-Servern mit der Option **Privater Zugriff (VNET-Integration)** über das Azure-Portal behandelt. Mit „Privater Zugriff (VNET-Integration)“ können Sie Ihre Flexible Server-Instanz in Ihrer eigenen [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)-Instanz bereitstellen. Azure Virtual Network-Instanzen ermöglichen eine private und sichere Netzwerkkommunikation. Bei privatem Zugriff sind die Verbindungen mit dem MySQL-Server auf Ihr virtuelles Netzwerk beschränkt. Weitere Informationen hierzu finden Sie unter [Privater Zugriff (VNET-Integration)](./concepts-networking-vnet.md#private-access-vnet-integration).

>[!Note]
>Sie können Ihren flexiblen Server bei der Servererstellung in einem virtuellen Netzwerk und Subnetz bereitstellen. Nachdem der flexible Server bereitgestellt wurde, können Sie ihn nicht in ein anderes virtuelles Netzwerk oder Subnetz verschieben oder auf *Öffentlicher Zugriff (zugelassene IP-Adressen)* umstellen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um eine Flexible Server-Instanz in einem virtuellen Netzwerk zu erstellen:

- Ein [virtuelles Netzwerk](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Das virtuelle Netzwerk und das Subnetz sollten sich in derselben Region und demselben Abonnement befinden wie Ihre Flexible Server-Instanz.

- [Delegierung eines Subnetzes](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) an **Microsoft.DBforMySQL/flexibleServers**. Diese Delegierung bedeutet, dass dieses Subnetz nur von Azure Database for MySQL Flexible Servers genutzt werden kann. Im delegierten Subnetz können sich keine anderen Azure-Ressourcentypen befinden.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Erstellen von Azure Database for MySQL Flexible Server-Instanzen in einem bereits vorhandenen virtuellen Netzwerk

1. Wählen Sie links oben im Portal **Ressource erstellen** (+) aus.
2. Wählen Sie **Datenbanken** > **Azure-Datenbank für MySQL** aus. Sie können auch **MySQL** in das Suchfeld eingeben, um nach dem Dienst zu suchen.
3. Wählen Sie **Flexible Server** als Bereitstellungsoption aus.
4. Füllen Sie das Formular **Grundlagen** aus.
5. Wechseln Sie zur Registerkarte **Netzwerk**.
6. Wählen Sie unter **Konnektivitätsmethode** die Option **Privater Zugriff (VNET-Integration)** aus. Wechseln Sie zum Abschnitt **Virtuelles Netzwerk**. Sie können entweder ein bereits vorhandenes *virtuelles Netzwerk* und *Subnetz* auswählen, das an *Microsoft.DBforMySQL/flexibleServers* delegiert ist, oder ein neues erstellen, indem Sie auf den Link *Virtuelles Netzwerk erstellen* klicken.
    > [!Note]
    > In der Dropdownliste werden nur virtuelle Netze und Subnetze in derselben Region und im selben Abonnement angezeigt. </br>
    > Das ausgewählte Subnetz wird an *Microsoft.DBforMySQL/flexibleServers* delegiert. Dies bedeutet, dass dieses Subnetz nur von flexiblen Azure Database for MySQL-Servern genutzt werden kann.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet-Integration":::

7. Erstellen Sie eine neue oder wählen Sie eine vorhandene **private DNS-Zone** aus.
    > [!NOTE]
    > Namen privater DNS-Zonen müssen mit `mysql.database.azure.com` enden. </br>
    > Wenn die Option zum Erstellen einer neuen privaten DNS-Zone nicht angezeigt wird, geben Sie den Servernamen auf der Registerkarte **Grundlagen** ein.</br>
    > Nachdem der flexible Server in einem virtuellen Netzwerk und Subnetz bereitgestellt wurde, können Sie ihn nicht mehr in „Öffentlicher Zugriff (zugelassene IP-Adressen)“ verschieben.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dnsconfiguration":::
8. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.
9. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit der Azure CLI](./how-to-manage-virtual-network-cli.md)
- Erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- Erfahren Sie mehr über [Virtuelle Netzwerke für Azure Database for MySQL Flexible Server](./concepts-networking-vnet.md#private-access-vnet-integration).
