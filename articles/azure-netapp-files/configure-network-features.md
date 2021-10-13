---
title: Konfigurieren von Netzwerkfeatures für ein Azure NetApp Files-Volume | Microsoft-Dokumentation
description: Es werden die Optionen für Netzwerkfeatures und die Konfiguration der Option „Netzwerkfeatures“ für ein Volume beschrieben.
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
ms.date: 10/04/2021
ms.custom: references_regions
ms.author: b-juche
ms.openlocfilehash: 6350084ef916132d7b4d77da00853836daeabccd
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533595"
---
# <a name="configure-network-features-for-an-azure-netapp-files-volume"></a>Konfigurieren von Netzwerkfeatures für ein Azure NetApp Files-Volume

Die Funktion **Netzwerkfeatures** ist für die öffentliche Vorschau verfügbar.  Mit dieser Funktion können Sie angeben, ob Sie VNet-Features für ein Azure NetApp Files-Volume verwenden möchten. Mit dieser Funktion können Sie die Option auf **_Standard_ *_ oder _* _Basic_** festlegen. Sie können die Einstellung angeben, wenn Sie ein neues NFS-, SMB- oder Dual-Protokoll-Volume erstellen. Ausführliche Informationen zu Netzwerkfeatures finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](azure-netapp-files-network-topologies.md).

In diesem Artikel erfahren Sie mehr über die Optionen und wie Sie Netzwerkfeatures konfigurieren.

## <a name="options-for-network-features"></a>Optionen für Netzwerkfeatures 

Für Netzwerkfeatures sind zwei Einstellungen verfügbar: 

* ***Standard***  
    Diese Einstellung aktiviert VNet-Features für das Volume.  

    Wenn Sie höhere IP-Grenzwerte oder VNet-Features, wie [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md), [benutzerdefinierte Routen](../virtual-network/virtual-networks-udr-overview.md#user-defined) oder zusätzliche Konnektivitätsmuster, benötigen, sollten Sie **Netzwerkfeatures** auf *Standard* festlegen.

* ***Grundlegend***  
    Diese Einstellung bietet reduzierte IP-Grenzwerte (<1000) und keine zusätzlichen VNet-Features für die Volumes.

    Sie sollten **Netzwerkfeatures** auf *Basic* festlegen, wenn Sie keine VNet-Features benötigen.  

## <a name="supported-regions"></a>Unterstützte Regionen 

Derzeit wird die Funktion „Netzwerkfeatures“ in den folgenden Regionen unterstützt: 

* USA Nord Mitte 

## <a name="considerations"></a>Überlegungen

* Unabhängig davon, wie Sie die Option „Netzwerkfeatures“ festlegen (*Standard* oder *Basic*), kann ein Azure VNet nur über ein an Azure NetApp Files delegiertes Subnetz verfügen. Siehe [Delegieren eines Subnetzes an Azure NetApp Files](azure-netapp-files-delegate-subnet.md#considerations). 
 
* Derzeit können Sie die Einstellung für Netzwerkfeatures nur während des Erstellungsprozesses eines neuen Volumes angeben. Sie können die Einstellung auf vorhandenen Volumes nicht ändern. 

* Sie können Volumes mit den Standard-Netzwerkfeatures nur erstellen, wenn die entsprechende [Azure-Region die Standard-Volumefunktion unterstützt](#supported-regions). 
    * Wenn die Standard-Volumefunktion für die Region unterstützt wird, wird im Feld „Netzwerkfeatures“ der Seite „Volume erstellen“ standardmäßig *Standard* verwendet. Sie können diese Einstellung in *Basic* ändern. 
    * Wenn die Standard-Volumefunktion für die Region nicht verfügbar ist, wird im Feld „Netzwerkfeatures“ der Seite „Volume erstellen“ standardmäßig *Basic* verwendet, und Sie können die Einstellung nicht ändern.

* Die Möglichkeit, Speicher zu finden, der mit dem gewünschten Netzwerkfeature-Typ kompatibel ist, hängt vom angegebenen VNet ab.  Wenn Sie aufgrund unzureichender Ressourcen kein Volume erstellen können, können Sie ein anderes VNet ausprobieren, für das kompatibler Speicher verfügbar ist.
  
## <a name="register-the-feature"></a>Registrieren der Funktion 

Die Funktion „Netzwerkfeatures“ befindet sich derzeit in der öffentlichen Vorschau. Wenn Sie diese Funktion zum ersten Mal verwenden, müssen Sie sie zuerst registrieren.

1.  Registrieren Sie die Funktion, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

2. Überprüfen Sie den Status der Funktionsregistrierung: 

    > [!NOTE]
    > Der **RegistrationState** kann bis zu 60 Minuten lang den Wert `Registering` aufweisen, bevor er sich in `Registered` ändert. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

## <a name="set-the-network-features-option"></a>Festlegen der Option „Netzwerkfeatures“

In diesem Abschnitt erfahren Sie, wie Sie die Option „Netzwerkfeatures“ festlegen. 

1. Während der Erstellung eines neuen [NFS](azure-netapp-files-create-volumes.md)-, [SMB](azure-netapp-files-create-volumes-smb.md)- oder [Dual-Protokoll](create-volumes-dual-protocol.md)-Volumes können Sie die Option **Netzwerkfeatures** unter der Registerkarte „Basic“ des Bildschirms „Volume erstellen“ auf **Basic** oder **Standard** festlegen.

    Der folgende Screenshot zeigt ein Beispiel für die Volumeerstellung für eine Region, die die Funktionen der Standard-Netzwerkfeatures unterstützt: 

    ![Screenshot: Volumeerstellung für Standard-Netzwerkfeatures](../media/azure-netapp-files/network-features-create-standard.png)

    Der folgende Screenshot zeigt ein Beispiel für die Volumeerstellung für eine Region, die die Funktionen der Standard-Netzwerkfeatures *nicht* unterstützt: 

    ![Screenshot: Volumeerstellung für Basic-Netzwerkfeatures](../media/azure-netapp-files/network-features-create-basic.png)

2. Bevor Sie die Volumeerstellung abschließen, können Sie die angegebene Einstellung für Netzwerkfeatures auf der Registerkarte **Überprüfen + erstellen** des Bildschirms „Volume erstellen“ anzeigen. Klicken Sie auf **Erstellen**, um die Volumeerstellung abzuschließen.

    ![Screenshot: Registerkarte „Überprüfen und erstellen“ der Volumeerstellung](../media/azure-netapp-files/network-features-review-create-tab.png)

3. Sie können auf **Volumes** klicken, um die Einstellung für Netzwerkfeatures für jedes Volume anzuzeigen:

    [ ![Screenshot: Seite „Volumes“ mit der Einstellung für Netzwerkfeatures](../media/azure-netapp-files/network-features-volume-list.png)](../media/azure-netapp-files/network-features-volume-list.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte  

* [Richtlinien für die Azure NetApp Files-Netzwerkplanung](azure-netapp-files-network-topologies.md)
* [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erstellen eines SMB-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Erstellen eines Volumes mit dualem Protokoll für Azure NetApp Files](create-volumes-dual-protocol.md) 