---
title: 'Tutorial: Konfigurieren der Routingpräferenz für eine VM – Azure-Portal'
description: In diesem Tutorial wird beschrieben, wie Sie mit dem Azure-Portal eine VM mit öffentlicher IP-Adresse und Routingpräferenz erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a06ec95b6970e4c3f8d4bde4cc180bf832f3a6a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369506"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Tutorial: Konfigurieren der Routingpräferenz für einen virtuellen Computer mit dem Azure-Portal 

In diesem Tutorial erfahren Sie, wie Sie die Routingpräferenz für eine VM konfigurieren. Der Internetdatenverkehr von der VM wird über das ISP-Netzwerk geleitet, wenn Sie **Internet** als Option für die Routingpräferenz auswählen. Das Standardrouting erfolgt über das globale Netzwerk von Microsoft.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers mit einer öffentlichen IP-Adresse und der Routingpräferenz **Internet**
> * Überprüfen, ob für die öffentliche IP-Adresse die Routingpräferenz **Internet** festgelegt wurde

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

In diesem Abschnitt erstellen Sie eine VM und eine öffentliche IP-Adresse. Wählen Sie während der Konfiguration der öffentlichen IP-Adresse die Option **Internet** als Routingpräferenz aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Portalsuchfeld den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** und dann **+ Virtueller Computer** aus.

4. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **TutorVMRoutePref-rg** ein. Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie die Option **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen2** aus. |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine Größe aus. |
    | **Administratorkonto** |   |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Übernehmen Sie die Standardeinstellung **RDP (3389)** . </br> _**Bei Produktionsworkloads wird vom Öffnen von Port 3389 über das Internet abgeraten**_. |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-virtual-machine.png" alt-text="Screenshot: Erstellen einer VM":::

5. Wählen Sie **Weiter: Datenträger** und dann **Weiter: Netzwerk** aus, oder wählen Sie die Registerkarte **Netzwerk** aus.

6. Geben Sie auf der Registerkarte „Netzwerk“ die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkschnittstelle** |   |
    | Virtuelles Netzwerk | Übernehmen Sie die Standardeinstellung **(neu) TutorVMRoutePref-rg-vnet**. |
    | Subnet | Übernehmen Sie die Standardeinstellung **(neu) Standard (10.1.0.0/24)** . |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myPublicIP** unter **Name** ein. </br> Wählen Sie unter **SKU** die Option **Standard** aus. </br> Wählen Sie unter **Routingpräferenz** die Option **Internet** aus. </br> Klicken Sie auf **OK**. |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-public-ip.png" alt-text="Screenshot: Erstellen einer öffentlichen IP-Adresse":::.

7. Klicken Sie auf **Überprüfen + erstellen**.

8. Klicken Sie auf **Erstellen**.

## <a name="verify-internet-routing-preference"></a>Überprüfen der Routingpräferenz „Internet“

In diesem Abschnitt suchen Sie nach der zuvor erstellten öffentlichen IP-Adresse und überprüfen, ob die Routingpräferenz „Internet“ festgelegt ist.

1. Geben Sie im Portalsuchfeld den Suchbegriff **Öffentliche IP-Adresse** ein. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

2. Wählen Sie unter **Öffentliche IP-Adressen** die Option **myPublicIP** aus.

3. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.

4. Vergewissern Sie sich, dass unter **Routingpräferenz** die Option **Internet** angezeigt wird. 

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/verify-routing-preference.png" alt-text="Screenshot: Überprüfen der Routingpräferenz „Internet“":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die öffentlichen IP-Adressen wie folgt:

1. Geben Sie oben im Portal im Suchfeld **Ressourcengruppe** ein.

2. Wählen Sie in den Suchergebnissen den Eintrag **Ressourcengruppen** aus.

3. Wählen Sie **TutorVMRoutePref-rg** aus.

4. Wählen Sie die Option **Ressourcengruppe löschen**.

5. Geben Sie **myResourceGroup** für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie eine VM mit gemischter Routingpräferenz erstellen:
> [!div class="nextstepaction"]
> [Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer](routing-preference-mixed-network-adapter-portal.md)

