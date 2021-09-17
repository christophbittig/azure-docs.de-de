---
title: Netzwerkoptionen des Azure VM Image Builder-Diensts
description: In diesem Artikel werden die Netzwerkoptionen für die Bereitstellung des Azure VM Image Builder-Diensts erläutert.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 1285f3673851e5784a5bc35b606e2dc30d7cfd09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691869"
---
# <a name="azure-image-builder-service-networking-options"></a>Netzwerkoptionen des Azure VM Image Builder-Diensts

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Sie müssen entscheiden, ob Azure VM Image Builder mit oder ohne einem vorhandenen VNET bereitgestellt werden soll.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Bereitstellen ohne Angabe eines vorhandenen VNETs

Wenn Sie kein vorhandenes VNET angeben, erstellt Azure VM Image Builder ein VNET und ein Subnetz in der Stagingressourcengruppe. Eine öffentliche IP-Adresse wird mit einer Netzwerksicherheitsgruppe verwendet, um eingehenden Datenverkehr an den Azure VM Image Builder-Dienst einzuschränken. Die öffentliche IP-Adresse ermöglicht den Kanal für Azure VM Image Builder-Befehle während der Imageerstellung. Sobald die Erstellung abgeschlossen ist, werden die VM, die öffentliche IP-Adresse, die Datenträger und das VNET gelöscht. Geben Sie keine VNET-Eigenschaften an, um diese Option zu verwenden.

## <a name="deploy-using-an-existing-vnet"></a>Bereitstellung mithilfe eines vorhandenen VNETs

Wenn Sie ein VNET und ein Subnetz angeben, stellt Azure VM Image Builder die Build-VM im von Ihnen ausgewählten VNET bereit. Sie können auch auf Ressourcen zugreifen, die in Ihrem VNET verfügbar sind. Sie können auch ein abgeschottetes VNET erstellen, das mit keinem anderen VNET verbunden ist. Wenn Sie ein VNET angeben, verwenden Azure VM Image Builder keine öffentliche IP-Adresse. Die Kommunikation zwischen dem Azure VM Image Builder-Dienst und der Build-VM erfolgt mithilfe der Azure Private Link-Technologie.

Weitere Informationen finden Sie in einem der folgenden Beispiele:

* [Verwenden von Azure VM Image Builder für Windows-VMs mit Zugriff auf ein vorhandenes Azure-VNET](../windows/image-builder-vnet.md)
* [Verwenden von Azure VM Image Builder für Linux-VMs mit Zugriff auf ein vorhandenes Azure-VNET](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Was ist Azure Private Link?

Azure Private Link stellt eine private Verbindung zwischen einem virtuellen Netzwerk und Azure-PaaS-Diensten (Platform-as-a-Service), kundeneigenen Diensten oder Diensten von Microsoft-Partnern her. Dadurch wird die Netzwerkarchitektur vereinfacht und die Verbindung zwischen Endpunkten in Azure wird geschützt, indem die Offenlegung von Daten im öffentlichen Internet verhindert wird. Weitere Informationen finden Sie in der [Dokumentation zu Private Link](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Erforderliche Berechtigungen für ein vorhandenes VNET

Der Azure VM Image Builder erfordert spezifische Berechtigungen für die Verwendung eines vorhandenen VNETs. Weitere Informationen finden Sie unter [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI](image-builder-permissions-cli.md) oder [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe von PowerShell](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Was wird während einer Imageerstellung bereitgestellt?

Bei Verwendung eines vorhandenen VNETs stellt Azure VM Image Builder eine zusätzliche VM (eine Proxy-VM) und eine Azure Load Balancer-Instanz bereit, die mit dem Private Link-Dienst verbunden sind. Der Datenverkehr vom Azure VM Image Builder-Dienst verläuft über Private Link an die Azure Load Balancer-Instanz. Die Azure Load Balancer-Instanz kommuniziert mit der Proxy-VM über Port 60001 (Linux) oder 60000 (Windows). Der Proxy leitet Befehle über Port 22 (Linux) oder 5986 (Windows) an die Build-VM weiter.

> [!NOTE]
> Das VNET muss sich in derselben Region wie der Azure VM Image Builder-Dienst befinden.
> 

### <a name="why-deploy-a-proxy-vm"></a>Gründe für die Bereitstellung einer Proxy-VM

Wenn eine VM ohne öffentliche IP-Adresse sich hinter einem internen Lastenausgleich befindet, verfügt diese nicht über Internetzugriff. Die für das VNET verwendete Azure Load Balancer-Instanz ist ein interner Lastenausgleich. Die Proxy-VM lässt den Internetzugriff für die Build-VM während Buildprozessen zu. Die zugeordneten Netzwerksicherheitsgruppen können dazu verwendet werden, den Zugriff auf die Build-VM einzuschränken.

Die zusätzlich zur Build-VM bereitgestellte Proxy-VM-Größe ist „Standard A1_v2“. Die Proxy-VM wird dazu verwendet, Befehle zwischen dem Azure VM Image Builder-Dienst und der Build-VM zu übermitteln. Die Eigenschaften der Proxy-VM, einschließlich der Größe und das Betriebssystem, können nicht geändert werden. Die Proxy-VM-Eigenschaften für Imageerstellungen können sowohl unter Windows als auch unter Linux nicht geändert werden.

### <a name="image-template-parameters-to-support-vnet"></a>Imagevorlagenparameter zur Unterstützung von VNETs
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Einstellung | BESCHREIBUNG |
|---------|---------|
| name | (Optional) Diese Einstellung gibt den Namen eines bereits vorhandenen virtuellen Netzwerks an. |
| subnetName | Diese Einstellung gibt den Namen des Subnetzes im angegebenen virtuellen Netzwerk an. Diese Einstellung muss und darf nur angegeben werden, wenn die Einstellung *Name* angegeben wird. |
| resourceGroupName | Diese Einstellung gibt den Namen der Ressourcengruppe an, die das angegebene virtuelle Netzwerk enthält. Diese Einstellung muss und darf nur angegeben werden, wenn die Einstellung *Name* angegeben wird. |

Der Private Link-Dienst erfordert eine IP-Adresse für das jeweilige VNET und Subnetz. Azure unterstützt Netzwerkrichtlinien für diese IP-Adressen derzeit nicht. Daher müssen Netzwerkrichtlinien für das Subnetz deaktiviert werden. Weitere Informationen finden Sie in der [Dokumentation zu Private Link](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>Prüfliste zur Verwendung Ihres VNETs

1. Lassen Sie die Kommunikation zwischen Azure Load Balancer und der Proxy-VM in einer Netzwerksicherheitsgruppe zu.
    * [Azure CLI-Beispiel](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell-Beispiel](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Deaktivieren Sie die Richtlinie für private Dienste im Subnetz.
    * [Azure CLI-Beispiel](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell-Beispiel](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Lassen Sie das Erstellen einer Azure Load Balancer-Instanz und das Hinzufügen von VMs zum VNET durch Azure VM Image Builder zu.
    * [Azure CLI-Beispiel](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell-Beispiel](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Lassen Sie das Lesen/Schreiben von Quellimages und das Erstellen von Images durch Azure VM Image Builder zu.
    * [Azure CLI-Beispiel](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell-Beispiel](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Stellen Sie sicher, dass Sie ein VNET in derselben Region wie der Azure VM Image Builder-Dienst verwenden.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über Azure Image Builder](../image-builder-overview.md).