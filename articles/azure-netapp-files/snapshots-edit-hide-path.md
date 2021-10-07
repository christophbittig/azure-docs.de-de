---
title: Bearbeiten der Option Ausblenden von dem Pfad der Momentaufnahme von Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt, wie Sie die Sichtbarkeit einer Momentaufnahme mit Azure NetApp Files kontrollieren können.
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 5b165bd2e4ca3ee6ac2d16bac7c4d5ff1117fdc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700207"
---
# <a name="edit-the-hide-snapshot-path-option-of-azure-netapp-files"></a>Bearbeiten der Option Ausblenden von dem Pfad der Momentaufnahme von Azure NetApp Files
Die Option „Momentaufnahmepfad ausblenden“ steuert, ob der Momentaufnahmepfad eines Volumes sichtbar ist. Während der Erstellung eines [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume)- oder [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)-Volumes können Sie angeben, ob der Momentaufnahmepfad ausgeblendet werden soll. Sie können die Option „Momentaufnahmepfad ausblenden“ anschließend nach Bedarf bearbeiten.  

> [!NOTE]
> Für ein [Zielvolume](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) bei der regionsübergreifenden Replikation ist die Einstellung „Momentaufnahmepfad ausblenden“ standardmäßig aktiviert, und die Einstellung kann nicht geändert werden. 

## <a name="steps"></a>Schritte

1. Wählen Sie das Volume aus, um die Optionseinstellung „Momentaufnahmepfad ausblenden“ eines Volumes anzuzeigen. Das Feld **Momentaufnahmepfad ausblenden** zeigt an, ob die Option aktiviert ist.   
    ![Screenshot, der das Feld „Momentaufnahmepfad ausblenden“ veranschaulicht.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Um die Option „Momentaufnahmepfad ausblenden“ zu bearbeiten, klicken Sie auf der Volumeseite auf **Bearbeiten**, und ändern Sie die Option **Momentaufnahmepfad ausblenden** nach Bedarf.   
    ![Screenshot, der die Option „Volumemomentaufnahme bearbeiten“ veranschaulicht.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="next-steps"></a>Nächste Schritte

* [Mehr lernen über Momentaufnahmen](snapshots-introduction.md)