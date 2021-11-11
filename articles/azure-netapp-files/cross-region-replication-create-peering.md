---
title: Erstellen der Volumereplikation für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt das Erstellen von Volumereplikationspeering für Azure NetApp Files, um regionsübergreifende Replikation einzurichten.
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
ms.date: 11/02/2021
ms.author: b-juche
ms.openlocfilehash: 8a937074cfc0cd4305ac5946bdb792d612c44f7b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465381"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>Erstellen der Volumereplikation für Azure NetApp Files

In diesem Artikel erfahren Sie, wie Sie regionsübergreifende Replikation einrichten, indem Sie Replikationspeering erstellen. 

Durch das Einrichten des Replikationspeerings können Sie Daten asynchron aus einem Azure NetApp Files-Volume (Quelle) in ein anderes Azure NetApp Files-Volume (Ziel) replizieren. Das Quellvolume und das Zielvolume müssen in separaten Regionen bereitgestellt sein. Der Servicelevel für den Zielkapazitätspool kann mit dem des Quellkapazitätspools entsprechen, Sie können aber auch einen anderen Servicelevel auswählen.   

Die Azure NetApp Files-Replikation unterstützt derzeit keine mehreren Abonnements. Alle Replikationen müssen unter einem einzelnen Abonnement ausgeführt werden.

Bevor Sie beginnen, stellen Sie sicher, dass Sie [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md) gelesen haben.  

## <a name="locate-the-source-volume-resource-id"></a>Auffinden der Ressourcen-ID des Quellvolumes  

Sie müssen die Ressourcen-ID des Quellvolumes abrufen, das Sie replizieren möchten. 

1. Wechseln Sie zu dem Quellvolume, und wählen Sie unter „Einstellungen“ **Eigenschaften** aus, um die Ressourcen-ID des Quellvolumes anzuzeigen.   
    ![Auffinden der Ressourcen-ID des Quellvolumes](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Kopieren Sie die Ressourcen-ID in die Zwischenablage.  Sie benötigen sie später.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Erstellen des Datenreplikationsvolumes (das Zielvolume)

Sie müssen ein Zielvolume erstellen, in das die Daten aus dem Quellvolume repliziert werden sollen.  Bevor Sie ein Zielvolume erstellen können, benötigen Sie ein NetApp-Konto und einen Kapazitätspool in der Zielregion. 

1. Das Zielkonto muss sich in einer anderen Region als das Quellvolume befinden. Erstellen Sie erforderlichenfalls anhand der Schritte in [Erstellen eines NetApp-Kontos](azure-netapp-files-create-netapp-account.md) ein NetApp-Konto in der Azure-Region, die für die Replikation verwendet werden solle.   
Sie können auch ein vorhandenes NetApp-Konto in einer anderen Region auswählen.  

2. Erstellen Sie erforderlichenfalls einen Kapazitätspool in dem neu erstellten NetApp-Konto, indem Sie die Schritte in [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md) ausführen.   

    Sie können auch einen vorhandenen Kapazitätspool auswählen, um das Replikationszielvolume zu hosten.  

    Der Servicelevel für den Zielkapazitätspool kann mit dem des Quellkapazitätspools entsprechen, Sie können aber auch einen anderen Servicelevel auswählen.

3. Delegieren Sie ein Subnetz in der Region, das für die Replikation verwendet werden soll, indem Sie den Schritten in [Delegieren eines Subnetzes an Azure NetApp Files](azure-netapp-files-delegate-subnet.md) folgen.

4. Erstellen Sie das Volume für die Datenreplikation, indem Sie im NetApp-Zielkonto unter „Speicherdienst“ die Option **Volumes** auswählen. Klicken Sie dann auf die Schaltfläche **+ Datenreplikation hinzufügen**.  

    ![Hinzufügen von Datenreplikation](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Füllen Sie auf der angezeigten Seite „Volume erstellen“ auf der Registerkarte **Grundlagen** die folgenden Felder aus:
    * Volumename
    * Kapazitätspool
    * Volumekontingent
        > [!NOTE] 
        > Das Volumekontingent (Größe) für das Zielvolume sollte die Größe des Quellvolumes widerspiegeln. Wenn Sie eine Größe angeben, die kleiner als das Quellvolume ist, wird die Größe des Zielvolumes automatisch der Größe des Quellvolumes angepasst. 
    * Virtuelles Netzwerk 
    * Subnet

    Ausführliche Informationen zu den Feldern finden Sie unter [Erstellen eines NFS-Volumes](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. Wählen Sie auf der Registerkarte **Protokoll** dasselbe Protokoll wie das des Quellvolumes aus.  
Stellen Sie für das NFS-Protokoll sicher, dass die Exportrichtlinienregeln die Anforderungen aller Hosts im Remotenetzwerk erfüllen, die auf den Export zugreifen werden.  

7. Erstellen Sie auf der Registerkarte **Tags** Schlüssel-Wert-Paare nach Bedarf.  

8. Fügen Sie auf der Registerkarte **Replikation** die Ressourcen-ID des Quellvolumes ein, die Sie unter [Auffinden der Ressourcen-ID des Quellvolumes](#locate-the-source-volume-resource-id) abgerufen haben, und wählen Sie dann den gewünschten Replikationszeitplan aus. Die Optionen für den Replikationszeitplan umfassen: alle 10 Minuten, stündlich und täglich.

    ![Erstellen der Volumereplikation](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Klicken Sie auf **Überprüfen + erstellen** und anschließend auf **Erstellen**, um das Datenreplikationsvolume zu erstellen.   

    ![Überprüfen und Erstellen der Replikation](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Autorisieren der Replikation vom Quellvolume  

Um die Replikation zu autorisieren, müssen Sie die Ressourcen-ID des Replikationszielvolumes abrufen und in das Feld „Autorisieren“ des Replikationsquellvolumes einfügen. 

1. Navigieren Sie im Azure-Portal zu „Azure NetApp Files“.

2. Wechseln Sie zum NetApp-Zielkonto und zum Zielkapazitätspool, in dem sich das Replikationszielvolume befindet.

3. Wählen Sie das Replikationszielvolume aus, wechseln Sie unter „Einstellungen“ zu **Eigenschaften**, und suchen Sie die **Ressourcen-ID** des Zielvolumes. Kopieren Sie die Ressourcen-ID des Zielvolumes in die Zwischenablage.

    ![Eigenschaften Ressourcen-ID](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. Wechseln Sie in Azure NetApp Files zum Replikationsquellkonto und zum Quellkapazitätspool. 

5. Suchen Sie das Replikationsquellvolume, und wählen Sie es aus Wechseln Sie unter „Speicherdienst“ zu **Replikation**, und klicken Sie auf **Autorisieren**.

    ![Autorisieren der Replikation](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. Fügen Sie die Ressourcen-ID des Zielreplikationsvolumes in das Feld „Autorisieren“ ein, die Sie in Schritt 3 abgerufen haben, und klicken Sie dann auf **OK**.

    > [!NOTE]
    > Aufgrund verschiedener Faktoren, z. B. des Zustands des Zielspeichers zu einem bestimmten Zeitpunkt, besteht wahrscheinlich ein Unterschied zwischen dem verwendeten Speicherplatz des Quellvolumens und dem auf dem Zielvolumen verwendeten Speicherplatz. <!-- ANF-14038 --> 

## <a name="next-steps"></a>Nächste Schritte  

* [Regionsübergreifende Replikation](cross-region-replication-introduction.md)
* [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md)
* [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md)
* [Metriken für die Volumereplikation](azure-netapp-files-metrics.md#replication)
* [Verwalten der Notfallwiederherstellung](cross-region-replication-manage-disaster-recovery.md)
* [Löschen von Volumereplikationen oder Volumes](cross-region-replication-delete.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)

