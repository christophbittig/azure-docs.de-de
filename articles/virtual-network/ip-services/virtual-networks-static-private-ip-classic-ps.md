---
title: Konfigurieren von privaten IP-Adressen für virtuelle Computer (klassisch) – Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie private IP-Adressen für virtuelle Computer (klassisch) mithilfe von PowerShell konfigurieren.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: allensu
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28291444583f197bb43a77ac063dc1495242fabb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368043"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurieren von privaten IP-Adressen für einen virtuellen Computer (klassisch) mithilfe von PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im Ressourcen-Manager-Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../../includes/virtual-networks-static-ip-scenario-include.md)]

Die folgenden PowerShell-Beispielbefehle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps)beschrieben.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Überprüfen der Verfügbarkeit einer bestimmten IP-Adresse
Wenn Sie beispielsweise überprüfen möchten, ob die IP-Adresse *192.168.1.101* in einem VNet namens *TestVnet* verfügbar ist, führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich den Wert für *IsAvailable* an:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Erwartete Ausgabe:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers
Das folgende PowerShell-Skript erstellt einen neuen Clouddienst namens *TestService*, ruft dann ein Image aus Azure ab, erstellt auf dessen Grundlage in dem neuen Clouddienst einen virtuellen Computer namens *DNS01*, platziert den virtuellen Computer in einem Subnetz namens *FrontEnd* und legt *192.168.1.7* als statische private IP-Adresse für den virtuellen Computer fest:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Erwartete Ausgabe:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich die Werte für *IpAddress* an, um Informationen zur statischen privaten IP-Adresse des virtuellen Computers zu erhalten, der mit dem obigen Skript erstellt wurde:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Erwartete Ausgabe:

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
InstanceUpgradeDomain       : 0
InstanceSize                : Small
HostName                    : rsR2-797
AvailabilitySetName         : 
DNSName                     : http://testservice000.cloudapp.net/
Status                      : Provisioning
GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
PublicIPAddress             : 
PublicIPName                : 
NetworkInterfaces           : {}
ServiceName                 : TestService
OperationDescription        : Get-AzureVM
OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
OperationStatus             : OK
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, um die statische private IP-Adresse zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Erwartete Ausgabe:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie folgenden Befehl aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Erwartete Ausgabe:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>Festlegen von IP-Adressen innerhalb des Betriebssystems

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist. Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die dem virtuellen Azure-Computer zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer innerhalb des Betriebssystems des virtuellen Computers zugewiesen werden.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [reservierte öffentliche IP-Adressen](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .
* Erfahren Sie mehr über [öffentliche IP-Adressen auf Instanzebene (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](/previous-versions/azure/reference/dn722420(v=azure.100)).