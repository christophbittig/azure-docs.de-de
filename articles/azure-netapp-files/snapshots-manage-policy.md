---
title: Verwalten von Momentaufnahmerichtlinien in Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird das Erstellen, Verwalten, Ändern und Löschen von Momentaufnahmerichtlinien mithilfe von Azure NetApp Files beschrieben.
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
ms.openlocfilehash: e4ccbd68ead83f682f39359c053e5a608d34903c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700204"
---
# <a name="manage-snapshot-policies-in-azure-netapp-files"></a>Verwalten von Momentaufnahmerichtlinien in Azure NetApp Files

[Momentaufnahmen](snapshots-introduction.md) ermöglichen die Zeitpunktwiederherstellung von Volumes. Mithilfe von Momentaufnahmerichtlinien können Sie die automatische Erstellung von [Volumemomentaufnahmen](snapshots-introduction.md) planen. Sie können eine Momentaufnahmenrichtlinie nach Bedarf auch ändern oder sogar löschen, wenn Sie sie nicht mehr benötigen.  

## <a name="create-a-snapshot-policy"></a>Erstellen einer Momentaufnahmenrichtlinie 

Eine Momentaufnahmenrichtlinie ermöglicht es Ihnen, die Häufigkeit der Erstellung von Momentaufnahmen in stündlichen, täglichen, wöchentlichen oder monatlichen Zyklen anzugeben. Außerdem müssen Sie die maximale Anzahl von Momentaufnahmen angeben, die für das Volume beibehalten werden soll.  

1.  Klicken Sie in der Ansicht für das NetApp-Konto auf **Momentaufnahmenrichtlinie**.

    ![Screenshot: Navigieren zur Momentaufnahmerichtlinie](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Legen Sie die Option „Richtlinienstatus“ im Fenster „Momentaufnahmenrichtlinie“ auf **Aktiviert** fest. 

3.  Klicken Sie auf die Registerkarte **Stündlich**, **Täglich**, **Wöchentlich** oder **Monatlich**, um Richtlinien für stündliche, tägliche, wöchentliche oder monatliche Momentaufnahmen zu erstellen. Bestimmen Sie die **Anzahl von Momentaufnahmen, die beibehalten werden sollen**.  

    Informationen zur maximalen Anzahl von Momentaufnahmen, die für ein Volume zulässig sind, finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md). 

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für stündliche Momentaufnahmen. 

    ![Screenshot: Richtlinie für stündliche Momentaufnahmen](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für tägliche Momentaufnahmen.

    ![Screenshot: Richtlinie für tägliche Momentaufnahmen](../media/azure-netapp-files/snapshot-policy-daily.png)

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für wöchentliche Momentaufnahmen.

    ![Screenshot: Richtlinie für wöchentliche Momentaufnahmen](../media/azure-netapp-files/snapshot-policy-weekly.png)

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für monatliche Momentaufnahmen.

    ![Screenshot: Richtlinie für monatliche Momentaufnahmen](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klicken Sie auf **Speichern**.  

Wiederholen Sie Schritt 3, wenn Sie zusätzliche Momentaufnahmenrichtlinien erstellen müssen.
Die von Ihnen erstellten Richtlinien werden auf der Seite „Momentaufnahmenrichtlinie“ angezeigt.

Wenn Sie möchten, dass die Richtlinie von einem Volume verwendet wird, müssen Sie [die Richtlinie auf das Volume anwenden](snapshots-manage-policy.md#apply-a-snapshot-policy-to-a-volume). 

## <a name="apply-a-snapshot-policy-to-a-volume"></a>Anwenden einer Momentaufnahmenrichtlinie auf ein Volume

Wenn Sie möchten, dass ein Volume eine von Ihnen erstellte Momentaufnahmenrichtlinie verwendet, müssen Sie die Richtlinie auf das Volume anwenden. 

Bei der regionsübergreifenden Replikation können Sie eine Momentaufnahmenrichtlinie nicht auf ein Zielvolume anwenden.  

1.  Navigieren Sie zur Seite **Volumes**, klicken Sie mit der rechten Maustaste auf das Volume, auf das Sie eine Momentaufnahmenrichtlinie anwenden möchten, und klicken Sie dann auf **Bearbeiten**.

    ![Screenshot: Kontextmenü für Volumes](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Wählen Sie unter **Momentaufnahmenrichtlinie** im Fenster „Bearbeiten“ eine Richtlinie aus, die für das Volume verwendet werden soll.  Klicken Sie auf **OK**, um die Richtlinie anzuwenden.  

    ![Screenshot: Menü für Momentaufnahmerichtlinie](../media/azure-netapp-files/snapshot-policy-edit.png) 

## <a name="modify-a-snapshot-policy"></a>Ändern einer Momentaufnahmenrichtlinie 

Sie können eine vorhandene Momentaufnahmenrichtlinie ändern, um den Richtlinienstatus, die Häufigkeit der Momentaufnahmen (stündlich, täglich, wöchentlich oder monatlich) oder die Anzahl von Momentaufnahmen zu ändern, die aufbewahrt werden sollen.  
 
1.  Klicken Sie in der Ansicht für das NetApp-Konto auf **Momentaufnahmenrichtlinie**.

2.  Klicken Sie mit der rechten Maustaste auf die Momentaufnahmenrichtlinie, die Sie ändern möchten, und wählen Sie dann **Bearbeiten** aus.

    ![Screenshot: Kontextmenü für Momentaufnahmerichtlinie](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Nehmen Sie die Änderungen im Fenster „Momentaufnahmenrichtlinie“ vor, das angezeigt wird, und klicken Sie dann auf **Speichern**. 

## <a name="delete-a-snapshot-policy"></a>Löschen einer Momentaufnahmenrichtlinie 

Sie können eine Momentaufnahmenrichtlinie löschen, die Sie nicht mehr beibehalten möchten.   

1.  Klicken Sie in der Ansicht für das NetApp-Konto auf **Momentaufnahmenrichtlinie**.

2.  Klicken Sie mit der rechten Maustaste auf die Momentaufnahmenrichtlinie, die Sie ändern möchten, und wählen Sie dann **Löschen** aus.

    ![Screenshot: Menüelement „Löschen“](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Klicken Sie auf **Ja**, um zu bestätigen, dass Sie die Momentaufnahmenrichtlinie löschen möchten.   

    ![Screenshot: Bestätigung zum Löschen der Momentaufnahmerichtlinie](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung für Momentaufnahmenrichtlinien](troubleshoot-snapshot-policies.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Weitere Informationen zu Momentaufnahmen](snapshots-introduction.md)