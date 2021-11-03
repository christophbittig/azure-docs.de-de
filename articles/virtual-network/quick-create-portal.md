---
title: 'Schnellstart: Erstellen eines virtuellen Netzwerks – Azure-Portal'
titleSuffix: Azure Virtual Network
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal ein virtuelles Netzwerk erstellen.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags: azure-resource-manager
ms.custom: mode-portal
ms.openlocfilehash: 1ba52ed17f5eddafe4489ddf9d4277ac301c3e15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074249"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Schnellstart: Erstellen eines virtuellen Netzwerks im Azure-Portal

In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal ein virtuelles Netzwerk erstellen. Dazu stellen Sie zwei virtuelle Computer (virtual machines, VMs) bereit. Anschließend nutzen Sie die sichere Kommunikation zwischen virtuellen Computern und stellen über das Internet eine Verbindung mit virtuellen Computern her. Ein virtuelles Netzwerk ist der grundlegende Baustein für Ihr privates Netzwerk in Azure. Über dieses Netzwerk können Azure-Ressourcen wie etwa virtuelle Computer sicher miteinander und mit dem Internet kommunizieren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Wählen Sie im Portal oben links die Option **Ressource erstellen** aus.

2. Geben Sie in das Suchfeld **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelles Netzwerk** aus.

3. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Erstellen** aus.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcengruppe | Wählen Sie **Neu erstellen**.  </br> Geben Sie **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myVNet** ein. |
    | Region | Wählen Sie **(USA) USA, Osten** aus. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Erstellen eines virtuellen Netzwerks: Azure-Portal" border="true":::

5. Wählen Sie die Registerkarte **IP-Adressen** oder unten auf der Seite die Schaltfläche **Weiter: IP-Adressen** aus.

6. Wählen Sie unter **IPv4-Adressraum** den vorhandenen Adressraum aus, und ändern Sie ihn in **10.1.0.0/16**.

7. Wählen Sie **+ Subnetz hinzufügen** aus, und geben Sie unter **Subnetzname** den Namen **MySubnet** und unter **Subnetzadressbereich** den Bereich **10.1.0.0/24** ein.

8. Wählen Sie **Hinzufügen**.

9. Wählen Sie die Registerkarte **Sicherheit** oder unten auf der Seite die Schaltfläche **Weiter: Sicherheit** aus.

10. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/24** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |

11. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie zwei virtuelle Computer im virtuellen Netzwerk:

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Wählen Sie **USA, Osten** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |    |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |
    |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Basic** aus.|
    | Public inbound ports network (Netzwerk mit öffentlichen Eingangsports) | Wählen Sie **Keine**. |
   
5. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM2** ein. |
    | Region | Wählen Sie **USA, Osten** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |    |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |
    |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **mySubnet** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Basic** aus.|
    | Public inbound ports network (Netzwerk mit öffentlichen Eingangsports) | Wählen Sie **Keine**. |
   
5. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Herstellen einer Verbindung mit myVM1

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um Ihre private VM zu verwalten. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

2. Wählen Sie den Namen Ihres privaten virtuellen Computers **myVM1** aus.

3. Wählen Sie auf der Menüleiste des virtuellen Computers die Option **Verbinden** und dann **Bastion** aus.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Herstellen einer Verbindung zwischen myVM1 und Azure Bastion" border="true":::

4. Wählen Sie auf der Seite **Verbinden** die blaue Schaltfläche **Bastion verwenden** aus.

5. Geben Sie auf der Seite **Bastion** den Benutzernamen und das Kennwort ein, die Sie zuvor für den virtuellen Computer erstellt haben.

6. Wählen Sie **Verbinden** aus.

## <a name="communicate-between-vms"></a>Kommunikation zwischen VMs

1. Öffnen Sie PowerShell in der Bastionverbindung von **myVM1**.

2. Geben Sie `ping myvm2` ein.

    Sie erhalten eine Meldung wie die folgende:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Trennen Sie die Bastionhostverbindung mit **myVM1**.

4. Führen Sie die Schritte unter [Herstellen einer Verbindung mit myVM1](#connect-to-myvm1) aus, stellen Sie jedoch eine Verbindung mit **myVM2** her.

5. Öffnen Sie PowerShell auf **myVM2**, und geben Sie `ping myvm1` ein.

    Sie erhalten etwa folgende Meldung:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Trennen Sie die Bastionhostverbindung mit **myVM2**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In dieser Schnellstartanleitung haben Sie ein virtuelles Standardnetzwerk und zwei virtuelle Computer erstellt. 

Sie haben über das Internet eine Verbindung mit einem der virtuellen Computer hergestellt und sicher zwischen den beiden virtuellen Computern kommuniziert.

Wenn Sie Ihre Arbeit mit dem virtuellen Netzwerk und den virtuellen Computern abgeschlossen haben, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen:

1. Suchen Sie nach der Ressourcengruppe **myResourceGroup**, und wählen Sie sie aus.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie **myResourceGroup** für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VNET-Einstellungen finden Sie unter [Erstellen, Ändern oder Löschen eines virtuellen Netzwerks](manage-virtual-network.md).

Weitere Informationen zu verschiedenen Arten der VM-Netzwerkkommunikation finden Sie unter [Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](tutorial-filter-network-traffic.md).
