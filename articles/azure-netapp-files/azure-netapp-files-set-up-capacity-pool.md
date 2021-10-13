---
title: Erstellen eines Kapazitätspools für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt die Vorgehensweise zum Erstellen eines Kapazitätspools für die Volumeerstellung.
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 3488b6807982f97cd5feef9b07a6dc7cbe180dcd
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407549"
---
# <a name="create-a-capacity-pool-for-azure-netapp-files"></a>Erstellen eines Kapazitätspools für Azure NetApp Files

Nach dem Erstellen eines Kapazitätspools können Sie Volumes in diesem Pool erstellen.  

## <a name="before-you-begin"></a>Voraussetzungen 

Sie müssen bereits ein NetApp-Konto erstellt haben.   

[Erstellen eines NetApp-Kontos](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Schritte 

1. Navigieren Sie zum Blatt für die Verwaltung Ihres NetApp-Kontos, und klicken Sie anschließend im Navigationsbereich auf **Kapazitätspools**.  
    
    ![Navigieren zu einem Kapazitätspool](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Klicken Sie auf **+ Pools hinzufügen**, um einen neuen Kapazitätspool zu erstellen.   
    Das Fenster für den neuen Kapazitätspool wird angezeigt.

3. Geben Sie für den neuen Kapazitätspool folgende Informationen an:  
   * **Name**  
     Geben Sie den Namen für den Kapazitätspool an.  
     Der Name des Kapazitätspools muss für jedes NetApp-Konto eindeutig sein.

   * **Servicelevel**   
     In diesem Feld wird die gewünschte Leistung des Kapazitätspools angezeigt.  
     Geben Sie die Dienstebene für den Kapazitätspool an: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium) oder [**Standard**](azure-netapp-files-service-levels.md#Standard).

    * **Größe**     
     Geben Sie die Größe des Kapazitätspools an, den Sie erwerben.        
     Die Mindestgröße eines Kapazitätspools beträgt 4 TiB. Die Poolgröße kann auf ein Vielfaches von 4 TiB festgelegt werden.   

   * **QoS**   
     Geben Sie an, ob für den Kapazitätspool der QoS-Typ **Manuell** oder **Automatisch** verwendet werden soll.  

     Informationen zu den QoS-Typen finden Sie unter [Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) und [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md).  

     > [!IMPORTANT] 
     > Wenn Sie **QoS-Typ** auf **Manuell** festlegen, kann dies nicht rückgängig gemacht werden. Sie können einen manuellen QoS-Kapazitätspool nicht auf die Verwendung des QoS-Typs „Automatisch“ umstellen. Sie können jedoch einen automatischen QoS-Kapazitätspool auf die Verwendung des QoS-Typs „Manuell“ umstellen. Weitere Informationen finden Sie unter [Ändern eines Kapazitätspools für die Verwendung der manuellen QoS](manage-manual-qos-capacity-pool.md#change-to-qos).   

    ![Neuer Kapazitätspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klicken Sie auf **Erstellen**.

## <a name="next-steps"></a>Nächste Schritte 

- [Speicherhierarchie](azure-netapp-files-understand-storage-hierarchy.md) 
- [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Seite mit den Preisen von Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Verwalten eines manuellen QoS-Kapazitätspools](manage-manual-qos-capacity-pool.md)
- [Delegieren eines Subnetzes für Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
