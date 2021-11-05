---
title: Konfigurieren von MetalLB über BGP in Azure Stack Edge
description: Hier wird beschrieben, wie Sie MetalLB über das Border Gateway Protocol für den Lastenausgleich auf Ihrem Azure Stack Edge-Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/24/2021
ms.author: alkohli
ms.openlocfilehash: df4f0170f6dde0995576b0244733dea4ab01515e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021501"
---
# <a name="configure-load-balancing-with-metallb-on-your-azure-stack-edge"></a>Konfigurieren des Lastenausgleichs mit MetalLB in Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie den Lastenausgleich auf Ihrem Azure Stack Edge-Gerät mithilfe von MetalLB über das Border Gateway Protocol (BGP) konfigurieren. 

## <a name="about-metallb-and-load-balancing"></a>Informationen zu MetalLB und Lastenausgleichen

MetalLB ist eine Lastenausgleichsimplementierung für Bare-Metal-Kubernetes-Cluster. MetalLB erfüllt zwei Funktionen: Die Implementierung weist den Kubernetes-Lastenausgleichsdiensten IP-Adressen aus einem konfigurierten Pool von IP-Adressen zu und kündigt dann dem externen Netzwerk die IP-Adresse an. MetalLB ermöglicht diese Funktionen mithilfe von Standardroutingprotokollen wie dem Address Resolution-Protokoll (ARP), Neighbor Discovery Protocol (NDP) oder Border Gateway Protocol (BGP). 

Weitere Informationen finden Sie unter [BGP-Modus für MetalLB](https://metallb.universe.tf/configuration/#bgp-configuratioN).

## <a name="metallb-on-azure-stack-edge"></a>MetalLB in Azure Stack Edge

Auf Ihrem Azure Stack Edge-Gerät sind mehrere Netzwerkkomponenten wie Calico, MetalLB und Core DNS installiert. MetalLB ist mit dem Kubernetes-Cluster verbunden, der auf Ihrem Azure Stack Edge-Gerät ausgeführt wird, und ermöglicht es Ihnen, Kubernetes-Dienste vom Typ `LoadBalancer` im Cluster zu erstellen.

Im BGP-Modus richten alle Computer im Cluster BGP-Peeringsitzungen mit Routern in der Nähe ein, die Sie steuern, und teilen diesen Routern mit, wie Datenverkehr an die Dienst-IPs weitergeleitet werden soll. MetalLB mit dem Border Gateway Protocol (BGP) ist nicht der Standardnetzwerkmodus für den Kubernetes-Cluster, der auf Ihrem Gerät ausgeführt wird. Um MetalLB über das BGP zu konfigurieren, legen Sie den ToR-Switch (Top-of-Rack) als Lastenausgleich fest und richten Peersitzungen ein. 

MetalLB im BGP-Modus kann so konfiguriert werden, dass niedrige Failoverzeiten erreicht werden, wenn Sie Geräte mit zwei Knoten verwenden. Diese Konfiguration ist komplexer als die Standardkonfiguration, da Sie möglicherweise keinen Zugriff auf den Top-of-Rack-Switch haben.

## <a name="configure-metallb"></a>Konfigurieren von MetalLB

Sie können MetalLB im BGP-Modus konfigurieren, indem Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts herstellen und dann bestimmte Cmdlets ausführen.

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Compute ist an einem Port des Geräts aktiviert. Dadurch wird ein virtueller Switch an diesem Port erstellt. 
    - Um Compute zu aktivieren, wechseln Sie auf der lokalen Benutzeroberfläche für Ihr Gerät zur Seite **Erweitertes Netzwerk**, und wählen Sie einen Port aus, für den Sie Compute aktivieren möchten. 
    - Aktivieren Sie auf der Seite **Netzwerkeinstellungen** den Port für Compute. **Wenden Sie die Einstellungen an**.
- Sie verfügen über verfügbare IPs im gleichen Subnetz des Ports, den Sie für Compute auf Ihrem Gerät aktiviert haben. 

### <a name="configuration"></a>Konfiguration

Für eine grundlegende Konfiguration für MetalLB mit BGP-Sitzung benötigen Sie die folgenden Informationen:

- Die Peer-IP-Adresse, mit der MetalLB eine Verbindung herstellen soll.
- Die autonome Systemnummer (Autonomous System Number, ASN) des Peers. Das BGP erfordert, dass Routen mit einer ASN für Peersitzungen angekündigt werden.
- Die ASN, die MetalLB verwenden sollte. ASNs sind 16-Bit-Zahlen zwischen 1 und 65534 und 32-Bit-Zahlen zwischen 131072 und 4294967294.

> [!IMPORTANT]
> Sie müssen Peers angeben, damit MetalLB im BGP-Modus funktioniert. Wenn keine BGP-Peers angegeben werden, wird MetalLB im Standardmodus der Ebene 2 ausgeführt. Weitere Informationen finden Sie unter [Ebene-2-Modus in MetalLB](https://metallb.universe.tf/concepts/layer2/). 


Führen Sie die folgenden Schritte aus, um MetalLB im BGP-Modus zu konfigurieren:

1. [Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)
 
1. Führen Sie das Cmdlet `Get-HcsExternalVirtualSwitch` aus, um den Namen des externen virtuellen Switches zu erhalten, den Sie für den BGP-Modus verwenden. Dieser virtuelle Switch wird erstellt, wenn Sie den Port für Compute aktiviert haben.

    ```powershell
    Get-HcsExternalVirtualSwitch
    ```
1. Führen Sie das Cmdlet `Set-HcsBGPPeer` aus, um eine BGP-Peersitzung zu erstellen.

    ```powershell
    Set-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -PeerAsn <ASN for the peer> -SelfAsn <Your ASN> -SwitchName <Name of virtual switch on the port enabled for compute> -HoldTimeInSeconds <Optional hold time in seconds> 
    ```
1. Nachdem Sie die Sitzung eingerichtet haben, führen Sie das Cmdlet `Get-HcsBGPPeers` aus, um die Peersitzungen abzurufen, die auf einem virtuellen Switch vorhanden sind.

    ```powershell
    Get-HcsBGPPeers -SwitchName <Name of virtual switch that you enabled for compute>
    ```
1. Führen Sie das Cmdlet `Remove-HcsBGPPeer` aus, um die Peersitzung zu entfernen. 

    ```powershell
    Remove-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -SwitchName <Name of virtual switch on the port enabled for compute>
    ```
1. Führen Sie `Get-HcsBGPPeers` aus, um zu überprüfen, ob die Peersitzung entfernt wurde.

Beispielausgabe: 

```powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $Name = "dbe-1csphq2.microsoftdatabox.com"
PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to
be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface
to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft
Support could result in an unsupported configuration.
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : False
EnabledForMgmt                : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 3cb2d0ae-6a7b-44cc-8a5d-8eac2d1c0436
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

Name                          : vSwitch2
InterfaceAlias                : {Port3, Port4}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : True
EnabledForMgmt                : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 8dd480c0-8f22-42b1-8621-d2a43f70690d
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

[dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsBGPPeer -PeerAddress 10.126.77.125 -PeerAsn 64512 -SelfAsn 64513 -SwitchName vSwitch1 -HoldTimeInSeconds 15
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1

PeerAddress   PeerAsn SelfAsn HoldTime
-----------   ------- ------- --------
10.126.77.125 64512   64513         15

[dbe-1csphq2.microsoftdatabox.com]: PS>Remove-HcsBGPPeer -PeerAddress 10.126.77.125 -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Kubernetes-Netzwerke auf Azure Stack Edge Pro GPU-Geräten](azure-stack-edge-gpu-kubernetes-networking.md).