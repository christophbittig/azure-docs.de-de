---
title: 'Tutorial: Erstellen eines NAT-Gateways: Azure-Portal'
titlesuffix: Azure Virtual Network NAT
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe des Azure-Portals ein NAT-Gateway erstellen und überprüfen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: c8375a7e7a404c257b80f48b8824f26bb3af309c
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162547"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutorial: Erstellen eines NAT-Gateways mithilfe des Azure-Portals

In diesem Tutorial erfahren Sie, wie Sie den Azure Virtual Network NAT-Dienst verwenden. Sie erstellen ein NAT-Gateway, um ausgehende Konnektivität für einen virtuellen Computer in Azure bereitzustellen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie ein virtuelles Netzwerk.
> * Erstellen Sie eine VM.
> * Erstellen Sie ein NAT-Gateway, und ordnen Sie es dem virtuellen Netzwerk zu.
> * Stellen Sie eine Verbindung mit dem virtuellen Computer her, und überprüfen Sie die NAT-IP-Adresse.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Virtuelles Netzwerk

Sie müssen zunächst die Ressourcengruppe und das virtuelle Netzwerk erstellen, damit Sie einen virtuellen Computer bereitstellen und das NAT-Gateway verwenden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

3. Klicken Sie auf **Erstellen**.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupNAT** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **(Europa) Europa, Westen** aus. |

5. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

6. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

7. Wählen Sie **+ Subnetz hinzufügen** aus. 

8. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **mySubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

9. Wählen Sie **Hinzufügen** aus.

10. Wählen Sie die Registerkarte **Sicherheit** .

11. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/24** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |

12. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

13. Klicken Sie auf **Erstellen**.

## <a name="nat-gateway"></a>NAT Gateway

Sie können mehrere öffentliche IP-Adressressourcen und/oder Präfixes für öffentliche IP-Adressen verwenden. Sie fügen eine Ressource für eine öffentliche IP-Adresse und eine NAT-Gatewayressource hinzu.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk> NAT-Gateway** aus, oder suchen Sie über das Suchfeld nach **NAT-Gateway**.

2. Klicken Sie auf **Erstellen**. 

3. Geben Sie auf der Registerkarte **Grundlagen** unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Wählen Sie Ihr Azure-Abonnement.                                  |
    | Ressourcengruppe   | Wählen Sie **myResourceGroupNAT** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myNATgateway** ein.                                    |
    | Region           | Wählen Sie **(Europa) Europa, Westen** aus.  |
    | Verfügbarkeitszone | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **10** ein. |

4. Wählen Sie die Registerkarte **Ausgehende IP-Adresse** oder unten auf der Seite die Schaltfläche **Weiter: Ausgehende IP-Adresse** aus.

5. Geben Sie auf der Registerkarte **Ausgehende IP-Adresse** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | **Einstellung** | **Wert** |
    | ----------- | --------- |
    | Öffentliche IP-Adressen | Wählen Sie **Neue öffentliche IP-Adresse erstellen** aus. </br> Geben Sie **myPublicIP** unter **Name** ein. </br> Klicken Sie auf **OK**. |

6. Wählen Sie die Registerkarte **Subnetz** oder unten auf der Seite die Schaltfläche **Weiter: Subnetz** aus.

7. Wählen Sie auf der Registerkarte **Subnetz** in der Dropdownliste **Virtuelles Netzwerk** die Option **myVNet** aus.

8. Aktivieren Sie das Kästchen neben **mySubnet**.

9. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

10. Klicken Sie auf **Erstellen**.
    
## <a name="virtual-machine"></a>Virtueller Computer

In diesem Abschnitt erstellen Sie einen virtuellen Computer, um das NAT-Gateway zu testen und die öffentliche IP-Adresse der ausgehenden Verbindung zu überprüfen.

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 

2. Geben Sie auf der Seite **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | **Einstellung** | **Wert** |
    | ----------- | --------- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroupNAT** aus. |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **(Europa) Europa, Westen** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie die Option **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen2** aus. |
    | Size | Wählen Sie **Standard_DS1_v2** aus. |
    | **Administratorkonto** |   |
    | Username | Geben Sie einen Benutzernamen für den virtuellen Computer ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Bestätigen Sie das Kennwort. |
    | **Regeln für eingehende Ports** |    |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |

3. Wählen Sie die Registerkarte **Datenträger** oder unten auf der Seite die Schaltfläche **Weiter: Datenträger** aus.

4. Übernehmen Sie auf der Registerkarte **Datenträger** die Standardeinstellung.

5. Wählen Sie die Registerkarte **Netzwerk** oder unten auf der Seite die Schaltfläche **Weiter: Netzwerk** aus.

6. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | **Einstellung** | **Wert** |
    | ----------- | --------- |
    | **Netzwerkschnittstelle** |   |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Basic** aus. |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |

7. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

8. Klicken Sie auf **Erstellen**.

## <a name="test-nat-gateway"></a>Testen des NAT-Gateways

In diesem Abschnitt testen Sie das NAT-Gateway. Sie ermitteln zunächst die öffentliche IP-Adresse des NAT-Gateways. Anschließend stellen Sie eine Verbindung mit dem virtuellen Testcomputer her und überprüfen die ausgehende Verbindung über das NAT-Gateway.
    
1. Suchen Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse für das NAT-Gateway. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend **myPublicIP** aus.

2. Notieren Sie sich die öffentliche IP-Adresse:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Ermitteln der öffentlichen IP-Adresse des NAT-Gateways" border="true":::

3. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myVM** (in der Ressourcengruppe **myResourceGroupNAT**) aus.

4. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

5. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

7. Öffnen Sie **Internet Explorer** auf **myTestVM**.

8. Geben Sie in der Adressleiste **https://whatsmyip.com** ein.

9. Vergewissern Sie sich, dass die angezeigte IP-Adresse mit der NAT-Gatewayadresse übereinstimmt, die Sie im vorherigen Schritt notiert haben:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer mit externer ausgehenden IP-Adresse" border="true":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die das virtuelle Netzwerk, den virtuellen Computer und das NAT-Gateway wie folgt:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

2. Wählen Sie die Ressourcengruppe **myResourceGroupNAT** aus.

3. Wählen Sie die Option **Ressourcengruppe löschen**.

4. Geben Sie **myResourceGroupNAT** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Virtual Network NAT finden Sie unter folgendem Link:
> [!div class="nextstepaction"]
> [Übersicht über Virtual Network NAT](nat-overview.md)
