---
title: Delegieren eines Subnetzes an Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Subnetz an Azure NetApp Files delegieren. Geben Sie das delegierte Subnetz an, wenn Sie ein Volume erstellen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/25/2021
ms.author: b-juche
ms.openlocfilehash: cdb184d4b96e4cfee2b5450f35c947efb768da9b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866976"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegieren eines Subnetzes an Azure NetApp Files 

Sie müssen ein Subnetze an Azure NetApp Files delegieren.   Wenn Sie ein Volume erstellen, müssen Sie das delegierte Subnetz angeben.

## <a name="considerations"></a>Überlegungen

* Der Assistent zum Erstellen eines neuen Subnetzes verwendet standardmäßig eine /24-Netzwerkmaske, die 251 IP-Adressen bereitstellt. Die Verwendung einer /28-Netzwerkmaske, die 11 nutzbare IP-Adressen bereitstellt, ist in den meisten Fällen ausreichend. In Szenarien wie SAP HANA, in denen viele Volumes und Speicherendpunkte erwartet werden, sollten Sie ein größeres Subnetz (z. B. /26-Netzwerkmaske) in Betracht ziehen. Sie können auch bei der vom Assistenten vorgeschlagenen Standardnetzwerkmaske /24 bleiben, wenn Sie nicht viele Client- oder VM-IP-Adressen in Ihrem Azure Virtual Network (VNet) reservieren müssen. Beachten Sie, dass die Netzwerkmaske des delegierten Netzwerks nach der ersten Erstellung nicht mehr geändert werden kann. 
* In jedem VNET kann nur ein Subnetz an Azure NetApp Files delegiert werden.   
   Azure ermöglicht es Ihnen, mehrere delegierte Subnetze in einem VNet zu erstellen.  Alle Versuche, ein neues Volume zu erstellen, schlagen jedoch fehl, wenn Sie mehr als ein delegiertes Subnetz verwenden.  
   Sie können nur über ein delegiertes Subnetz in einem VNet verfügen. Ein NetApp-Konto kann Volumes in mehreren VNets bereitstellen, die jeweils über ihre eigenen delegierte Subnetze verfügen.  
* Sie können im delegierten Subnetz keine Netzwerksicherheitsgruppe und keinen Dienstendpunkt festlegen. Dadurch würde bei der Subnetzdelegierung ein Fehler auftreten.
* Der Zugriff auf einem Volume aus einem globalen virtuellen Peeringnetzwerk wird zurzeit nicht unterstützt.
* [Benutzerdefinierte Routen](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (User-defined Route, UDR) und Netzwerksicherheitsgruppen (Network Security Group, NSG) werden auf delegierten Subnetzen für Azure NetApp Files nicht unterstützt. Sie können UDRs und NSGs jedoch auf andere Subnetze anwenden, die sich sogar im gleichen VNET befinden wie das an Azure NetApp Files delegierte Subnetz.  
   Azure NetApp Files erstellt eine Systemroute zum delegierten Subnetz. Die Route wird in **Effektive Routen** in der Routingtabelle angezeigt, wenn Sie dies zur Problembehandlung benötigen.

## <a name="steps"></a>Schritte

1.  Wechseln Sie im Azure-Portal zum Blatt **Virtuelle Netzwerke**, und wählen Sie das virtuelle Netzwerk aus, das Sie für Azure NetApp Files verwenden möchten.    

1. Wählen Sie auf dem Blatt „Virtuelles Netzwerk“ die Option **Subnetze** aus, und klicken Sie auf die Schaltfläche **+Subnetz**. 

1. Erstellen Sie ein neues Subnetz, das für Azure NetApp Files verwendet werden soll, indem Sie die folgenden erforderlichen Felder auf der Seite „Subnetz hinzufügen“ ausfüllen:
    * **Name**: Geben Sie den Namen des Subnetzes an.
    * **Adressbereich**: Geben Sie den IP-Adressbereich an.
    * **Subnetzdelegierung**: Wählen Sie **Microsoft.NetApp/volumes** aus. 

      ![Subnetzdelegierung](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Sie können ein Subnetz auch beim [Erstellen eines Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md) erstellen und delegieren. 

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erfahren Sie mehr über die Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md)
