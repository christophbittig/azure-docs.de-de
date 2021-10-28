---
title: Erstellen eines virtueller Switches für Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Installationen zum Erstellen eines virtuellen Switches für Azure IoT Edge für Linux unter Windows
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e9b47d3cfdb648a63156164f2341b05b4eeec4a1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234772"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Erstellung eines virtueller Switches für Azure IoT Edge für Linux unter Windows
Azure IoT Edge für Linux unter Windows verwendet einen virtuellen Switch auf dem Hostcomputer, um mit dem virtuellen Computer zu kommunizieren. Windows Desktopversionen umfassen einen Standardswitch, der verwendet werden kann, Windows Server allerdings nicht. Bevor Sie IoT Edge für Linux unter Windows auf einem Windows Server-Gerät bereitstellen können, müssen Sie einen virtuellen Switch erstellen. Sie können bei Bedarf diesen Leitfaden verwenden, um einen benutzerdefinierten virtuellen Switch zu erstellen. 

In diesem Artikel erfahren Sie, wie Sie einen virtuellen Switch auf einem Windows-Gerät erstellen, um IoT Edge für Linux unter Windows mit den folgenden Schritten zu installieren:
- Erstellen eines virtuellen Switches
- Erstellen einer NAT-Tabelle
- Installieren und Einrichten eines DHCP-Servers

## <a name="prerequisites"></a>Voraussetzungen
- Ein Windows-Gerät. Informationen zu unterstützten Windows-Versionen finden Sie unter [Betriebssysteme](support.md#operating-systems).
- Auf dem Windows-Gerät installierte Hyper-V-Rolle. Weitere Informationen zum Aktivieren von Hyper-V finden Sie unter [Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows Gerät](./how-to-provision-single-device-linux-on-windows-symmetric.md?tabs=powershell#prerequisites).

## <a name="create-virtual-switch"></a>Erstellen des virtuellen Switches 
Die folgenden Schritte in diesem Abschnitt sind eine allgemeine Anleitung für die Erstellung eines virtuellen Switches. Stellen Sie sicher, dass die Konfiguration des virtuellen Switches mit Ihrer Netzwerkumgebung übereinstimmt.

1. Öffnen Sie PowerShell in einer Sitzung mit erhöhten Rechten.

2. Überprüfen Sie die virtuellen Switches auf dem Windows-Host, und stellen Sie sicher, dass kein virtueller Switch vorhanden ist, der verwendet werden kann. Ausführliche Informationen finden Sie unter [Get-VMSwitch (Hyper-V)](/powershell/module/hyper-v/get-vmswitch). 

   ```powershell
   Get-VMSwitch
   ```

   Wenn bereits ein virtueller Switch mit dem Namen **Default Switch** erstellt wurde und Sie keinen benutzerdefinierten virtuellen Switch benötigen, sollten Sie IoT Edge für Linux unter Windows installieren können, ohne die restlichen Schritte in dieser Anleitung zu befolgen.

3. Erstellen Sie einen neuen VM-Switch mit einem Namen, und geben Sie **Intern** oder **Privat** ein. Geben Sie zum Erstellen eines **externen** virtuellen Switches entweder den Parameter **NetAdapterInterfaceDescription** oder **NetAdapterName** an. Mit diesen Parametern wird der Typ des virtuellen Switches implizit auf **External** festgelegt. Ausführliche Informationen und weitere Anweisungen finden Sie unter [New-VMSwitch (Hyper-V)](/powershell/module/hyper-v/new-vmswitch) und [Erstellen eines virtuellen Switches für virtuelle Hyper-V-Computer](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines).
   ```powershell
   New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
   ```

4. Rufen Sie den Schnittstellenindex des erstellten Switches ab. Ausführliche Informationen finden Sie unter [Get-NetAdapter (NetAdapter)](/powershell/module/netadapter/get-netadapter). 
   ```powershell
   (Get-NetAdapter -Name '*{switchName}*').ifIndex
   ```

5. Verwenden Sie den Schnittstellenindex aus dem vorherigen Schritt, um das IP-Adress-Oktett des erstellten Switchnetzwerkadapters abzurufen. Ausführliche Informationen finden Sie unter [Get-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/get-netipaddress). 
   ```powershell
   Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {ifIndex}
   ```

6. Erstellen Sie mithilfe der IP-Adressfamilie und des Schnittstellenindexes aus den vorherigen Schritten die neue Gateway-IP-Adresse, und legen Sie sie fest.  Wenn z. B. die IPv4-Adresse des virtuellen Netzwerkswitchadapters xxx.xxx.xxx.yyy lautet, können Sie gatewayIp wie folgt festlegen: xxx.xxx.xxx.1. Ausführliche Informationen finden Sie unter [New-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/new-netipaddress).
   ```powershell
   New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {ifIndex}
   ```

7. Erstellen Sie ein NAT-Objekt (Network Address Translation), das eine interne Netzwerkadresse in ein externes Netzwerk übersetzt. Verwenden Sie die gleiche IPv4-Adressfamilie aus den vorherigen Schritten. Wenn z. B. die IPv4-Adresse des virtuellen Netzwerkswitchadapters xxx.xxx.xxx.yyy lautet, können Sie natIp wie folgt festlegen: xxx.xxx.xxx.0. Ausführliche Informationen finden Sie unter [New-NetNat (NetNat)](/powershell/module/netnat/new-netnat). 
   ```powershell
   New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
   ```

## <a name="create-dhcp-server"></a>Erstellen des DHCP-Servers 

>[!WARNING]
>Für die Bereitstellung eines DHCP-Servers in einer Unternehmensnetzwerkumgebung ist möglicherweise eine Autorisierung erforderlich. Überprüfen Sie, ob die Konfiguration des virtuellen Switches den Richtlinien Ihres Unternehmensnetzwerks entspricht. Weitere Informationen finden Sie im Leitfaden [Bereitstellen von DHCP mit Windows PowerShell](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps). 

1. Überprüfen Sie, ob das DHCP-Serverfeature auf dem Gerät installiert ist. Suchen Sie nach der Spalte **Installationsstatus**.
   ```powershell
   Get-WindowsFeature -Name 'DHCP'
   ```

2. Wenn es nicht installiert ist, können Sie es mit dem folgenden Befehl installieren.
   ```powershell
   Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
   ```

3. Fügen Sie den DHCP-Server den lokalen Standardsicherheitsgruppen hinzu, und starten Sie den Server neu.
   ```powershell
   netsh dhcp add securitygroups
   Restart-Service dhcpserver
   ```

4. Konfigurieren Sie den DHCP-Serverbereich. Ausführliche Informationen finden Sie unter [Add-DhcpServerv4Scope (DhcpServer)](/powershell/module/dhcpserver/add-dhcpserverv4scope).  Der DHCP-Serverbereich von IP-Dateien wird durch **startIp** und **endIp** bestimmt. Wenn beispielsweise 100 Adressen verfügbar sein sollen, dann müssen nach der IPv4-Adresse xxx.xxx.xxx.yyy des virtuellen Netzwerkswitchadapters aus Schritt 5 startIp = xxx.xxx.xxx.100, endIp = xxx.xxx.xxx.200 und subnetMask = 255.255.255.0 lauten.
   ```powershell
   Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask {subnetMask} -State Active
   ```

5. Weisen Sie schließlich dem DHCP-Server das NAT-Objekt und gatewayIp zu, und starten Sie den Server neu, um die Konfiguration zu laden.
   ```powershell
   Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
   Restart-service dhcpserver
   ```

## <a name="next-steps"></a>Nächste Schritte
Führen Sie die Schritte unter [Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows-Gerät](how-to-provision-single-device-linux-on-windows-symmetric.md) aus, um ein Gerät mit IoT Edge für Linux unter Windows einzurichten.
