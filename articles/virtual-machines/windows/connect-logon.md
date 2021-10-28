---
title: Herstellen einer Verbindung mit der Windows Server-VM
description: Erfahren Sie, wie Sie unter Verwendung des Azure-Portals und des Resource Manager-Bereitstellungsmodells eine Verbindung mit einem virtuellen Windows-Computer herstellen und sich bei diesem Computer anmelden.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e09a94270ee440f462e538492a13bde263c6163
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234055"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer

**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 


Verwenden Sie die Schaltfläche **Verbinden** im Azure-Portal, um eine Remotedesktopsitzung (RDP) von einem Windows-Desktop zu starten. Zunächst stellen Sie eine Verbindung mit dem virtuellen Computer her, dann melden Sie sich an.

Um von einem Mac aus eine Verbindung mit einem virtuellen Windows-Computer herzustellen, müssen Sie einen RDP-Client für Mac installieren, z.B. [Microsoft-Remotedesktop](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Verbinden mit dem virtuellen Computer
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), um eine Verbindung mit einem virtuellen Computer herzustellen. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.
2. Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.
3. Wählen Sie oben auf der Seite für den virtuellen Computer die Option **Verbinden** aus.
4. Wählen Sie auf der Seite **Mit virtuellem Computer verbinden** die Option **RDP** und anschließend die entsprechende **IP-Adresse** und **Portnummer** aus. In den meisten Fällen sollten die Standard-IP-Adresse und der Standardport verwendet werden. Klicken Sie auf **RDP-Datei herunterladen**. Wenn für den virtuellen Computer eine Just-In-Time-Richtlinie festgelegt wurde, müssen Sie zuerst die Schaltfläche **Zugriff anfordern** auswählen, um Zugriff anzufordern, bevor Sie die RDP-Datei herunterladen können. Weitere Informationen zur Just-In-Time-Richtlinie finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe der Just-In-Time-Richtlinie](../../security-center/security-center-just-in-time.md).
5. Öffnen Sie die heruntergeladene RDP-Datei, und klicken Sie auf **Verbinden**, wenn Sie dazu aufgefordert werden. Es wird eine Warnung mit dem Hinweis angezeigt, dass die `.rdp`-Datei von einem unbekannten Herausgeber stammt. Dies entspricht dem erwarteten Verhalten. Klicken Sie im Fenster **Remotedesktopverbindung** auf **Verbinden**, um fortzufahren.
   
    ![Screenshot einer Warnung zu einem unbekannten Herausgeber](./media/connect-logon/rdp-warn.png)
3. Wählen Sie im Fenster **Windows-Sicherheit** die Option **Weitere Optionen** und dann **Anderes Konto verwenden** aus. Geben Sie die Anmeldeinformationen eines Kontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.
   
     **Lokales Konto**: Hierbei handelt es sich in der Regel um den Benutzernamen und das Kennwort des lokalen Kontos, den bzw. das Sie beim Erstellen des virtuellen Computers angegeben haben. In diesem Fall ist die Domäne der Name des virtuellen Computers. Das Eingabeformat lautet *VM-Name*&#92;*Benutzername*.  
   
    **In die Domäne eingebundener virtueller Computer**: Wenn der virtuelle Computer zu einer Domäne gehört, geben Sie den Benutzernamen im Format *Domäne*&#92;*Benutzername* ein. Das Konto muss außerdem entweder zur Gruppe „Administratoren“ gehören oder über Remotezugriffsrechte für den virtuellen Computer verfügen.
   
    **Domänencontroller**: Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.
4. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen und den Anmeldevorgang abzuschließen.
   
   ![Screenshot mit einer Meldung zur Überprüfung der Identität des virtuellen Computers](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Wenn die Schaltfläche **Verbinden** im Portal ausgeblendet ist und keine [ExpressRoute](../../expressroute/expressroute-introduction.md)- oder [Site-to-Site-VPN](../../vpn-gateway/tutorial-site-to-site-portal.md)-Verbindung mit Azure besteht, müssen Sie eine öffentliche IP-Adresse erstellen und dem virtuellen Computer zuweisen, damit Sie RDP nutzen können. Weitere Informationen finden Sie unter [Öffentliche IP-Adressen in Azure](../../virtual-network/ip-services/public-ip-addresses.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Herstellen einer Verbindung mit dem virtuellen Computer über PowerShell

 

Wenn Sie PowerShell verwenden und das Azure PowerShell-Modul installiert haben, können Sie die Verbindung auch über das Cmdlet `Get-AzRemoteDesktopFile` herstellen, wie unten dargestellt.

Dieses Beispiel startet die RDP-Verbindung unverzüglich. Dabei werden ähnliche Eingabeaufforderungen angezeigt wie weiter oben.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Sie können die RDP-Datei auch zur zukünftigen Verwendung speichern.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Nächste Schritte
Wenn beim Herstellen einer Verbindung Probleme auftreten, lesen Sie den Artikel [Problembehandlung bei Remotedesktopverbindungen](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).