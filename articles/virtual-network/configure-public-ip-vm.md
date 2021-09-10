---
title: Verwalten einer öffentlichen IP-Adresse mit einer Azure-VM
titleSuffix: Azure Virtual Network
description: Erfahren Sie mehr über die Verwendung einer öffentlichen IP-Adresse mit Azure Virtual Machines und über das Ändern der Konfiguration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 2271786f2f69e6894637ad3d7dd67f8415943a11
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439343"
---
# <a name="manage-a-public-ip-address-with-an-azure-virtual-machine"></a>Verwalten einer öffentlichen IP-Adresse mit einer Azure-VM

Öffentliche IP-Adressen sind in zwei SKUs verfügbar. „Standard“ und „Basic“. Die Features der IP-Adresse richten sich nach der Auswahl der SKU. Die SKU legt die Ressourcen fest, denen die IP-Adresse zugeordnet werden kann.  

Azure Virtual Machines ist der wichtigste Computedienst in Azure. Kunden können Linux- oder Windows-VMs erstellen. Eine öffentliche IP-Adresse kann einer VM für eingehende Verbindungen mit der VM zugewiesen werden. 

Für die Konfiguration einer VM ist keine öffentliche IP-Adresse erforderlich.

In diesem Artikel wird erläutert, wie Sie eine Azure-VM mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. Sie erfahren, wie Sie einer VM eine öffentliche IP-Adresse zuordnen. Sie werden die IP-Adresse ändern. Abschließend erfahren Sie, wie die öffentliche IP-Adresse entfernt wird.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Zwei öffentliche IP-Adressen der Standard-SKU in Ihrem Abonnement. Die IP-Adresse kann keinen Ressourcen zugeordnet werden. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse im Azure-Portal](create-public-ip-portal.md).
    - Benennen Sie die neuen öffentlichen IP-Adressen für die Beispiele in diesem Artikel mit **myStandardPublicIP-1** und **myStandardPublicIP-2**.
- Eine öffentliche IP-Adresse der Standard-SKU mit der Routingpräferenz **Internet** in Ihrem Abonnement. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse mit der Routingpräferenz **Internet** finden Sie unter [Konfigurieren der Routingpräferenz für eine öffentliche IP-Adresse mithilfe des Azure-Portals](./routing-preference-portal.md).
    - Benennen Sie die neue öffentliche IP-Adresse für das Beispiel in diesem Artikel mit **myStandardPublicIP-3**.
## <a name="create-virtual-machine-existing-public-ip"></a>Erstellen einer VM mit einer vorhandenen öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie eine VM. Sie wählen die IP-Adresse aus, die Sie in den Voraussetzungen als öffentliche IP-Adresse für die VM erstellt haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein.

3. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

4. Wählen Sie **+ Hinzufügen** und dann **+ Virtueller Computer** aus.

5. Geben Sie unter **VM erstellen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupVM** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. | 
    | Verfügbarkeitsoptionen | Wählen Sie die Option **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen1** aus. |
    | Azure Spot-Instanz  | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine Größe für die VM aus. |
    | **Administratorkonto** |   |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Bestätigen Sie das Kennwort. |
    | **Regeln für eingehende Ports** |   |
    | Öffentliche Eingangsports | Übernehmen Sie die Standardeinstellung **Ausgewählte Ports zulassen**. |
    | Eingangsports auswählen | Übernehmen Sie die Standardeinstellung **RDP (3389)** . |

6. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.

7. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkschnittstelle** |   |
    | Virtuelles Netzwerk | Übernehmen Sie die Standardeinstellung **(neu) myResourceGroupVM-vnet**. |
    | Subnet | Übernehmen Sie die Standardeinstellung **(neu) Standard (10.1.0.0/24)** . |
    | Öffentliche IP-Adresse | Wählen Sie **myStandardPublicIP-1** aus. |
    | NIC-Netzwerksicherheitsgruppe | Übernehmen Sie die Standardeinstellung **Basic**. |
    | Öffentliche Eingangsports | Übernehmen Sie die Standardeinstellung **Ausgewählte Ports zulassen**. |
    | Eingangsports auswählen | Übernehmen Sie die Standardeinstellung **RDP (3389)** . |

6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Dies ist eine einfache Bereitstellung einer Azure-VM. Informationen zur erweiterten Konfiguration und Einrichtung finden Sie unter [Schnellstart: Erstellen einer Windows-VM im Azure-Portal](../virtual-machines/windows/quick-create-portal.md).
>
> Weitere Informationen zu Azure Virtual Machines finden Sie unter [Windows-VMs in Azure](../virtual-machines/windows/overview.md).

## <a name="change-public-ip-address"></a>Ändern einer öffentlichen IP-Adresse

In diesem Abschnitt ändern Sie die öffentliche IP-Adresse, die der standardmäßigen öffentlichen IP-Konfiguration der VM zugeordnet ist.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein.

2. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie unter **Virtuelle Computer** die Option **myVM** aus.

4. Wählen Sie unter **Einstellungen** in **myVM** die Option **Netzwerk** aus.

5. Wählen Sie unter **Netzwerk** die **Netzwerkschnittstelle** der VM aus. Dem Namen der Netzwerkschnittstelle wird der Name der VM vorangestellt und eine Zufallszahl angehängt.  In diesem Beispiel wird **myvm793** verwendet.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Auswählen der Netzwerkschnittstelle" border="true":::

6. Wählen Sie unter **Einstellungen** für die Netzwerkschnittstelle die Option **IP-Konfigurationen** aus.

7. Wählen Sie unter **IP-Konfigurationen** die Option **ipconfig1** aus.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="Auswählen der IP-Konfiguration zum Ändern der IP-Adresse" border="true":::

1. Wählen Sie unter **Öffentliche IP-Adresse** von **ipconfig1** die Option **myStandardPublicIP-2** aus.

7. Wählen Sie **Speichern** aus.

## <a name="add-public-ip-configuration"></a>Hinzufügen einer öffentlichen IP-Konfiguration

In diesem Abschnitt fügen Sie der VM eine öffentliche IP-Konfiguration hinzu. 

Weitere Informationen zum Hinzufügen mehrerer IP-Adressen finden Sie unter [Zuweisen von mehreren IP-Adressen zu virtuellen Computern mithilfe des Azure-Portals](./virtual-network-multiple-ip-addresses-portal.md). 

Weitere Informationen zur Verwendung beider Routingpräferenztypen finden Sie unter [Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer](./routing-preference-mixed-network-adapter-portal.md).

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein.

2. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie unter **Virtuelle Computer** die Option **myVM** aus.

4. Wählen Sie unter **Einstellungen** in **myVM** die Option **Netzwerk** aus.

5. Wählen Sie unter **Netzwerk** die **Netzwerkschnittstelle** der VM aus. Dem Namen der Netzwerkschnittstelle wird der Name der VM vorangestellt und eine Zufallszahl angehängt.  In diesem Beispiel wird **myvm793** verwendet.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Auswählen der Netzwerkschnittstelle" border="true":::

6. Wählen Sie unter **Einstellungen** für die Netzwerkschnittstelle die Option **IP-Konfigurationen** aus.

7. Wählen Sie unter **IP-Konfigurationen** die Option **+ Hinzufügen** aus.

8. Geben Sie unter **Name** den Namen **ipconfig2** ein.

9. Klicken Sie unter **Öffentliche IP-Adresse** auf **Zuordnen**.

10. Wählen Sie unter **Öffentliche IP-Adresse** die Option **myStandardPublicIP-3** aus.

11. Klicken Sie auf **OK**.

## <a name="remove-public-ip-address-association"></a>Entfernen der Zuordnung öffentlicher IP-Adressen

In diesem Abschnitt entfernen Sie die öffentliche IP-Adresse aus der Netzwerkschnittstelle. Die VM ist nach diesem Vorgang für externe Verbindungen nicht mehr verfügbar.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein.

2. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie unter **Virtuelle Computer** die Option **myVM** aus.

4. Wählen Sie unter **Einstellungen** in **myVM** die Option **Netzwerk** aus.

5. Wählen Sie unter **Netzwerk** die **Netzwerkschnittstelle** der VM aus. Dem Namen der Netzwerkschnittstelle wird der Name der VM vorangestellt und eine Zufallszahl angehängt.  In diesem Beispiel wird **myvm793** verwendet.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="Auswählen der Netzwerkschnittstelle" border="true":::

6. Wählen Sie unter **Einstellungen** für die Netzwerkschnittstelle die Option **IP-Konfigurationen** aus.

7. Wählen Sie unter **IP-Konfigurationen** die Option **ipconfig1** aus.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="Auswählen der IP-Konfiguration zum Ändern der IP-Adresse" border="true":::

8. Klicken Sie unter **Einstellungen für öffentliche IP-Adressen** auf **Zuordnung aufheben**.

9. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine VM erstellen und eine vorhandene öffentliche IP-Adresse verwenden. Sie haben die öffentliche IP-Adresse der Standard-IP-Konfiguration geändert. Schließlich haben Sie der Firewall eine öffentliche IP-Konfiguration mit der Routingpräferenz „Internet“ hinzugefügt.

- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md).
