---
title: 'Tutorial: Konfigurieren des Peerings zwischen Azure Route Server und der virtuellen Quagga-Netzwerkappliance'
description: In diesem Tutorial erfahren Sie, wie Sie eine Azure Route Server-Instanz konfigurieren und sie mittels Peering mit einer virtuellen Quagga-Netzwerkappliance (Network Virtual Appliance, NVA) verbinden.
author: duongau
ms.author: duau
ms.service: route-server
ms.topic: tutorial
ms.date: 08/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbeee212b8ed7284780083166af34285f2d44a5e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823951"
---
# <a name="tutorial-configure-peering-between-azure-route-server-and-quagga-network-virtual-appliance"></a>Tutorial: Konfigurieren des Peerings zwischen Azure Route Server und der virtuellen Quagga-Netzwerkappliance

In diesem Tutorial erfahren Sie, wie Sie eine Azure Route Server-Instanz in einem virtuellen Netzwerk bereitstellen und eine BGP-Peeringverbindung mit einer virtuellen Quagga-Netzwerkappliance herstellen. Sie stellen ein virtuelles Netzwerk mit fünf Subnetzen bereit. Ein Subnetz ist für die Azure Route Server-Instanz und ein weiteres für die Quagga-NVA vorgesehen. Die Quagga-NVA wird zum Austauschen von Routen mit Route Server konfiguriert. Abschließend testen Sie, ob Routen in Azure Route Server und auf der Quagga-NVA ordnungsgemäß ausgetauscht werden.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks mit fünf Subnetzen
> * Bereitstellen einer Azure Route Server-Instanz
> * Bereitstellen eines virtuellen Computers, auf dem Quagga ausgeführt wird
> * Konfigurieren des Route Server-Peerings
> * Überprüfen erlernter Routen

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Sie benötigen ein virtuelles Netzwerk für die Bereitstellung von Azure Route Server und der Quagga-NVA. Jede Bereitstellung hat ihr eigenes dediziertes Subnetz.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen** aus, und suchen Sie nach **Virtuelles Netzwerk**. Klicken Sie anschließend auf **Erstellen**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-new-virtual-network.png" alt-text="Screenshot: Erstellen einer neuen Ressource für das virtuelle Netzwerk":::

1. Geben Sie auf der Registerkarte *Grundlagen* der Seite *Virtuelles Netzwerk erstellen* die folgenden Informationen ein, oder wählen Sie sie aus. Wählen Sie anschließend **Weiter: IP-Adressen >** aus.

    | Einstellungen | Wert |
    | -------- | ----- | 
    | Subscription | Wählen Sie das Abonnement für diese Bereitstellung aus. |
    | Resource group | Wählen Sie für diese Bereitstellung eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. | 
    | Name | Geben Sie einen Namen für das virtuelle Netzwerk ein. In diesem Tutorial wird *myVirtualNetwork* verwendet.
    | Region | Wählen Sie die Region aus, in der dieses virtuelle Netzwerk bereitgestellt wird. In diesem Tutorial wird *USA, Westen* verwendet.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-basics-tab.png" alt-text="Screenshot: Einstellungen auf der Registerkarte „Grundlagen“ für das virtuelle Netzwerk":::

1. Legen Sie auf der Registerkarte **IP-Adressen** den *Adressraum des virtuellen Netzwerks* auf **10.1.0.0/16** fest. Konfigurieren Sie dann die folgenden fünf Subnetze:

    | Subnetzname | Subnetzadressbereich |
    | ----------- | -------------------- |
    | RouteServerSubnet | 10.1.1.0/25 |
    | subnet1 | 10.1.2.0/24 |
    | subnet2 | 10.1.3.0/24 |
    | subnet3 | 10.1.4.0/24 |
    | GatewaySubnet | 10.1.5.0/24 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-ip-addresses.png" alt-text="Screenshot: Einstellungen der IP-Adressen für das virtuelle Netzwerk":::

1. Wählen Sie **Überprüfen + erstellen** und nach erfolgreicher Prüfung **Erstellen** aus.

## <a name="create-the-azure-route-server"></a>Erstellen der Azure Route Server-Instanz

Route Server wird verwendet, um mit der NVA zu kommunizieren und virtuelle Netzwerkrouten über eine BGP-Peeringverbindung auszutauschen.

1. Gehe zu https://aka.ms/routeserver.

1. Wählen Sie **+ Create new route server** (+ Neuen Routenserver erstellen) aus.

   :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Screenshot: Landing Page von Route Server":::

1. Geben Sie auf der Seite **Routenserver erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellungen | Wert |
    | -------- | ----- |
    | Subscription | Wählen Sie das gleiche Abonnement aus, in dem das virtuelle Netzwerk im vorherigen Abschnitt erstellt wurde. | 
    | Resource group | Wählen Sie die vorhandene Ressourcengruppe *myRouteServerRG* aus. |
    | Name | Geben Sie den Route Server-Namen *myRouteServer* ein. |
    | Region | Wählen Sie die Region **USA, Westen** aus. |
    | Virtual Network | Wählen Sie das virtuelle Netzwerk *myVirtualNetwork* aus. |
    | Subnet | Wählen Sie das zuvor erstellte Subnetz *RouteServerSubnet (10.1.0.0/25)* aus. |
    | Öffentliche IP-Adresse | Erstellen Sie eine neue öffentliche Standard-IP-Adresse, oder wählen Sie eine vorhandene öffentliche Standard-IP-Adresse aus, die mit Route Server verwendet werden soll. Mit dieser IP-Adresse wird die Konnektivität mit dem Back-End-Dienst sichergestellt, der die Route Server-Konfiguration verwaltet. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/route-server-basics-tab.png" alt-text="Screenshot: Registerkarte „Grundlagen“ für die Route Server-Erstellung":::

1. Wählen Sie **Überprüfen + erstellen** und nach erfolgreicher Prüfung **Erstellen** aus. Die Bereitstellung der Route Server-Instanz dauert etwa 15 Minuten.

## <a name="create-quagga-network-virtual-appliance"></a>Erstellen einer virtuellen Quagga-Netzwerkappliance

Zum Konfigurieren der virtuellen Quagga-Netzwerkappliance müssen Sie einen virtuellen Linux-Computer bereitstellen.

1. Navigieren Sie im Azure-Portal zu **+ Ressource erstellen > Compute > Virtueller Computer**. Klicken Sie anschließend auf **Erstellen**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-virtual-machine.png" alt-text="Screenshot: Seite zum Erstellen eines neuen virtuellen Computers":::

1. Geben Sie auf der Registerkarte *Grundlagen* die folgenden Informationen an, oder wählen Sie sie aus:

    | Einstellungen | Wert |
    | -------- | ----- |
    | Subscription | Wählen Sie das gleiche Abonnement aus, in dem auch zuvor das virtuelle Netzwerk bereitgestellt wurde. |
    | Resource group | Wählen Sie die vorhandene Ressourcengruppe *myRouteServerRG* aus. |
    | Name des virtuellen Computers | Geben Sie den Namen **Quagga** ein. |
    | Region | Wählen Sie die Region **USA, Westen** aus. |
    | Image | Wählen Sie **Ubuntu Server 18.04 LTS – Gen1** aus. |
    | Size | Wählen Sie **Standard_B2s - 2vcpus, 4GiB memory** (Standard_B2s – 2 vCPUs, 4 GiB Arbeitsspeicher) aus. |
    | Authentifizierungsart | Wählen Sie **Kennwort** aus. |
    | Username | Geben Sie *azureuser* ein. Verwenden Sie nicht *quagga* als Benutzername, andernfalls funktioniert das Setupskript in einem späteren Schritt nicht. |
    | Kennwort | Geben Sie das gewünschte Kennwort ein, und bestätigen Sie es. |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **SSH (22)** aus. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen eines neuen virtuellen Computers" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab-expanded.png":::
    
1. Wählen Sie auf der Registerkarte **Netzwerk** die folgenden Netzwerkeinstellungen aus:

    | Einstellungen | Wert |
    | -------- | ----- |
    | Virtual Network | Wählen Sie **myVirtualNetwork** aus. |
    | Subnet | Wählen Sie **subnet3 (10.1.4.0/24)** aus. |
    | Öffentliche IP-Adresse | Behalten Sie den Standard bei. |
    | NIC-Netzwerksicherheitsgruppe | Behalten Sie den Standard bei. **Basic** |
    | Öffentliche Eingangsports | Behalten Sie den Standard bei. Aktivieren Sie die Option **Ausgewählte Ports zulassen**. |
    | Eingangsports auswählen | Übernehmen Sie den Standardwert. **SSH (22)** . |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab.png" alt-text="Screenshot: Registerkarte „Netzwerk“ zum Erstellen eines neuen virtuellen Computers" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab-expanded.png":::

1. Wählen Sie **Überprüfen + erstellen** und nach erfolgreicher Prüfung **Erstellen** aus. Die Bereitstellung des virtuellen Computers dauert ungefähr zehn Minuten.

1. Navigieren Sie nach der Bereitstellung des virtuellen Computers zu den Netzwerkeinstellungen des virtuellen Quagga-Computers, und wählen Sie die Netzwerkschnittstelle aus.

     :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-network-settings.png" alt-text="Screenshot: Seite „Netzwerk“ des virtuellen Quagga-Computers":::

1. Wählen Sie unter *Einstellungen* die Option **IP-Konfiguration** und anschließend **ipconfig1** aus.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-ip-configuration.png" alt-text="Screenshot: Seite „IP-Konfiguration“ des virtuellen Quagga-Computers":::

1. Ändern Sie die Zuweisung von *Dynamisch* in **Statisch** und die IP-Adresse von *10.1.4.4* in **10.1.4.10**. Diese IP-Adresse wird in [diesem Skript](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh) verwendet. Dieses Skript wird in einem späteren Schritt ausgeführt. Wenn Sie eine andere IP-Adresse verwenden möchten, aktualisieren Sie die IP-Adresse im Skript. Wählen Sie **Speichern** aus, um die IP-Konfiguration des virtuellen Computers zu aktualisieren.

1. Stellen Sie mithilfe von [Putty](https://www.putty.org/) eine Verbindung mit dem virtuellen Quagga-Computer her. Verwenden Sie dazu die öffentliche IP-Adresse und die Anmeldeinformationen, die zum Erstellen des virtuellen Computers verwendet wurden.

1. Geben Sie nach der Anmeldung `sudo su` ein, um zum Administrator zu wechseln und dadurch Fehler beim Ausführen des Skripts zu vermeiden. Kopieren Sie [dieses Skript](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh), und fügen Sie es in die Putty-Sitzung ein. Das Skript konfiguriert den virtuellen Computer mit Quagga sowie weitere Netzwerkeinstellungen. Aktualisieren Sie das Skript entsprechend Ihrer Netzwerkumgebung, bevor Sie es auf dem virtuellen Computer ausführen. Es dauert einige Minuten, bis das Skript das Setup abgeschlossen hat.

## <a name="configure-route-server-peering"></a>Konfigurieren des Route Server-Peerings

1. Wechseln Sie zu der Route Server-Instanz, die Sie im vorherigen Schritt erstellt haben.

1. Wählen Sie unter *Einstellungen* die Option **Peers** aus. Wählen Sie anschließend **+ Hinzufügen** aus, um einen neuen Peer hinzuzufügen.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peers.png" alt-text="Screenshot: Seite „Peers“ für Route Server":::

1. Geben Sie auf der Seite *Add Peer* (Peer hinzufügen) die folgenden Informationen ein, und wählen Sie dann **Hinzufügen** aus, um die Konfiguration zu speichern:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie einen Namen zum Identifizieren des Peers ein. **Quagga** |
    | ASN | Geben Sie die ASN für die Quagga-NVA ein. Die im Skript definierte ASN lautet **65001**. |
    | IPv4-Adresse | Geben Sie die private IP-Adresse für den virtuellen Computer der Quagga-NVA ein. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/add-peer.png" alt-text="Screenshot: Seite zum Hinzufügen von Peers":::

1. Die Seite „Peers“ sollte nach dem Hinzufügen eines Peers wie folgt aussehen:

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peer-configured.png" alt-text="Screenshot: Konfigurierter Peer":::
    
## <a name="check-learned-routes"></a>Überprüfen erlernter Routen
1. Verwenden Sie den folgenden Befehl, um die von Route Sever erlernten Routen zu überprüfen:

    ```azurepowershell-interactive
    $routes = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        PeerName = 'Quagga'
    }
    Get-AzRouteServerPeerLearnedRoute @routes | ft
    ```

    Die Ausgabe sollte wie folgt aussehen: 

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/routes-learned.png" alt-text="Screenshot: Von Route Server erlernte Routen":::

1. Geben Sie zum Überprüfen der von der Quagga-NVA erlernten Routen `vtysh` gefolgt von `show ip bgp` ein. Die Ausgabe sollte wie folgt aussehen:

    ```
    root@Quagga:/home/azureuser# vtysh

    Hello, this is Quagga (version 1.2.4).
    Copyright 1996-2005 Kunihiro Ishiguro, et al.

    Quagga# show ip bgp
    BGP table version is 0, local router ID is 10.1.4.10
    Status codes: s suppressed, d damped, h history, * valid, > best, = multipath,
                  i internal, r RIB-failure, S Stale, R Removed
    Origin codes: i - IGP, e - EGP, ? - incomplete

        Network          Next Hop            Metric LocPrf Weight Path
        10.1.0.0/16      10.1.1.4                               0 65515 i
                         10.1.1.5                               0 65515 i
    *> 10.100.1.0/24    0.0.0.0                  0         32768 i
    *> 10.100.2.0/24    0.0.0.0                  0         32768 i
    *> 10.100.3.0/24    0.0.0.0                  0         32768 i
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Route Server-Instanz und alle zugeordneten Ressourcen nicht mehr benötigen, können Sie die Ressourcengruppe **myRouteServerRG** löschen.

:::image type="content" source="./media/tutorial-configure-route-server-with-quagga/delete-resource-group.png" alt-text="Screenshot: Schaltfläche „Ressourcengruppe löschen“":::

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um mehr über die Problembehandlung für Route Server zu erfahren:
> [!div class="nextstepaction"]
> [Beheben von Problemen mit Azure Route Server](troubleshoot-route-server.md)
