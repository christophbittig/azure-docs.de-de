---
title: 'Erstellen einer VM mit einer statischen privaten IP-Adresse: Azure-Portal'
description: Erfahren Sie, wie Sie eine VM mit einer statischen privaten IP-Adresse über das Azure-Portal erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eec4e9e07f19130732219c0932f66a1cfa0fa2b7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368186"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-portal"></a>Erstellen einer VM mit einer statischen privaten IP-Adresse über das Azure-Portal

Einem virtuellen Computer (VM) wird automatisch eine private IP-Adresse aus einem Bereich zugewiesen, den Sie angeben. Dieser Bereich basiert auf dem Subnetz, in dem der virtuelle Computer bereitgestellt wird. Der virtuelle Computer behält die Adresse bei, bis er gelöscht wird. Azure weist dynamisch die nächste verfügbare private IP-Adresse aus dem Subnetz zu, in dem Sie einen virtuellen Computer erstellen. Weisen Sie der VM eine statische IP-Adresse zu, wenn Sie eine bestimmte IP-Adresse im Subnetz wünschen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Gehen Sie folgendermaßen vor, um eine VM, ein virtuelles Netzwerk und ein Subnetz zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie in **Virtual Machines** die Option **+ Erstellen** und dann **+ VM** aus.

4. Geben Sie unter **VM erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **(USA) USA, Osten 2** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie die Option **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen2** aus. |
    | Azure Spot-Instanz | Lassen Sie die Option deaktiviert. |
    | Size | Wählen Sie eine Größe aus. |
    | **Administratorkonto** |   |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **RDP (3389)** aus. |

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/create-vm.png" alt-text="Screenshot: Erstellen einer VM":::

    > [!WARNING]
    > Port 3389 ist ausgewählt, um den Remotezugriff auf den virtuellen Windows Server-Computer über das Internet zu aktivieren. Beim Verwalten von Produktionsworkloads wird vom Öffnen von Port 3389 für das Internet abgeraten. </br> Informationen zum sicheren Zugriff auf virtuelle Azure-Computer finden Sie unter **[Was ist Azure Bastion?](../../bastion/bastion-overview.md)**

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Übernehmen Sie den Standardnetzwerknamen. |
    | Subnet | Übernehmen Sie die Standard-Subnetzkonfiguration. |
    | Öffentliche IP-Adresse | Übernehmen Sie die Standardkonfiguration für öffentliche IP-Adressen. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Basic** aus. |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **RDP (3389)** aus. |

5. Klicken Sie auf **Überprüfen + erstellen**. 
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="change-private-ip-address-to-static"></a>Ändern der privaten IP-Adresse in statisch

In diesem Abschnitt ändern Sie die private IP-Adresse für den zuvor erstellten virtuellen Computer von **dynamisch** in **statisch**.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie in **Virtual Machines** die VM **myVM** aus.

3. Wählen Sie unter **Einstellungen** in **myVM** die Option **Netzwerk** aus.

4. Wählen Sie unter **Netzwerk** den Namen der Netzwerkschnittstelle neben **Netzwerkschnittstelle** aus. In diesem Beispiel lautet der Name der Netzwerkschnittstelle **myvm472**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-nic.png" alt-text="Screenshot: Auswahl der Netzwerkschnittstelle":::

5. Wählen Sie in den Einstellungen der Netzwerkschnittstelle unter **Einstellungen** die Option **IP-Konfigurationen** aus.

6. Wählen Sie auf der Seite **IP-Konfigurationen** die Option **ipconfig1** aus.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-ip-configuration.png" alt-text="Screenshot: Auswahl der IP-Konfiguration":::

7. Wählen Sie unter **Zuweisung** die Option **Statisch** aus. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-static-assignment.png" alt-text="Screenshot: Auswahl der statischen Zuweisung":::

    > [!NOTE]
    > Wenn Sie nach Auswählen von **Speichern** feststellen, dass die Zuweisung immer noch auf **Dynamisch** festgelegt ist, wird die eingegebene IP-Adresse bereits verwendet. Probieren Sie eine andere IP-Adresse.

Um die IP-Adresse wieder in „Dynamisch“ zu ändern, legen Sie die Zuweisung Ihrer privaten IP-Adresse auf **Dynamisch** fest, und klicken Sie dann auf **Speichern**.

> [!WARNING]
> Im Betriebssystem einer VM dürfen Sie die *private* IP-Adresse, die der Azure-VM zugewiesen ist, nicht statisch zuweisen. Nehmen Sie die statische Zuweisung einer privaten IP-Adresse nur dann vor, wenn es notwendig ist, wie z. B. beim [Zuweisen vieler IP-Adressen zu VMs](virtual-network-multiple-ip-addresses-portal.md). 
>
>Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, stellen Sie sicher, dass sie mit der privaten IP-Adresse übereinstimmt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann Konnektivität mit der VM verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld **Suche** die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.

2. Wählen Sie die Option **Ressourcengruppe löschen**.

3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen **myResourceGroup** ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie im folgenden Artikel mehr über [statische öffentliche IP-Adressen](public-ip-addresses.md#ip-address-assignment).

- Lesen Sie mehr über [öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses) in Azure.

- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).

- Lesen Sie mehr über [private IP-Adressen](private-ip-addresses.md) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.

