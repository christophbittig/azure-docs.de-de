---
title: Verwalten von Sicherungsrichtlinien für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt, wie Sie eine Sicherungsrichtlinie für Azure NetApp Files Volumes ändern oder anhalten.
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
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 71f5c09fed1a733b4e04664aade5a69c3a99aae0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229973"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>Verwalten von Sicherungsrichtlinien für Azure NetApp Files 

Eine Momentaufnahmerichtlinienkonfiguration ist für [richtlinienbasierte Sicherungen](backup-configure-policy-based.md) von Azure NetApp Files erforderlich, um ein Volume automatisch zu sichern. Nachdem Sie Azure NetApp Files-Sicherungen mithilfe einer Sicherungsrichtlinie eingerichtet haben, können Sie eine Sicherungsrichtlinie bei Bedarf ändern oder anhalten.  

Manuelle Sicherungen sind von Änderungen in der Sicherungsrichtlinie nicht betroffen.

## <a name="modify-a-backup-policy"></a>Ändern einer Sicherungsrichtlinie   

Sie können eine vorhandene Azure NetApp Files-Sicherungsrichtlinie nach Bedarf ändern, um sicherzustellen, dass Sie über eine ordnungsgemäße Sicherungsabdeckung für Azure NetApp Files-Volumes verfügen.  Wenn Sie z. B. die Anzahl der vom Dienst geschützten beibehaltenen Sicherungen ändern müssen, können Sie die Azure NetApp Files-Sicherungsrichtlinie für das Volume ändern, um die Anzahl der beizubehaltenden Wiederherstellungen zu überarbeiten. 

So ändern Sie die Einstellungen der Sicherungsrichtlinie   

1. Navigieren Sie zu **Sicherungen**.  

2. Wählen Sie **Sicherungsrichtlinien** aus, klicken Sie auf die drei Punkte (`…`) rechts neben einer Sicherungsrichtlinie und dann auf **Bearbeiten**.

    ![Screenshot des kontextbezogenen Menüs von „Sicherungsrichtlinien“.](../media/azure-netapp-files/backup-policies-edit.png)

3. Aktualisieren Sie im Fenster „Sicherungsrichtlinie ändern“ die Anzahl der Wiederherstellungen, die Sie für tägliche, wöchentliche und monatliche Sicherungen behalten möchten. Geben Sie den Namen der Sicherungsrichtlinie ein, um die Aktion zu bestätigen. Klicken Sie auf **Speichern**.  

    ![Screenshot des Fensters „Sicherungsrichtlinie ändern“.](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > Nachdem Sicherungen aktiviert und für die geplante Häufigkeit wirksam wurden, können Sie die Anzahl der Sicherungsaufbewahrungen nicht mehr in `0` ändern. Für die Sicherungsrichtlinie ist eine Mindestanzahl von `1` Aufbewahrung erforderlich. Ausführliche Informationen finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md).  

## <a name="suspend-a-backup-policy"></a>Anhalten einer Sicherungsrichtlinie  

Eine Sicherungsrichtlinie kann angehalten werden, damit sie keine neuen Sicherungsvorgänge für die zugeordneten Volumes mehr ausführt. Mit dieser Aktion können Sie Sicherungen für den Fall vorübergehend anhalten, dass vorhandene Sicherungen beibehalten, aber nicht aufgrund der Versionsverwaltung eingestellt werden müssen.   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>Anhalten einer Sicherungsrichtlinie für alle Volumes, die der Richtlinie zugeordnet sind

1. Navigieren Sie zu **Sicherungen**.

2. Wählen Sie **Sicherungsrichtlinien** aus, klicken Sie auf die drei Punkte (`…`) rechts neben einer Sicherungsrichtlinie und dann auf **Bearbeiten**. 

3. Schalten Sie den **Richtlinienstatus** auf **Deaktiviert** um, geben Sie zur Bestätigung den Richtliniennamen ein, und klicken Sie auf **Speichern**. 

    ![Screenshot des Fensters „Sicherungsrichtlinie ändern“ mit Richtlinienstatus „Deaktiviert“.](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>Anhalten einer Sicherungsrichtlinie für ein bestimmtes Volume 

1. Wechseln Sie zu **Volumes**. 
2. Wählen Sie das spezifische Volume aus, dessen Sicherungen Sie anhalten möchten.
3. Wählen Sie **Konfigurieren** aus.
4. Schalten Sie auf der Seite „Sicherungen konfigurieren“ den **Richtlinienstatus** auf **Anhalten** um, geben Sie zur Bestätigung den Volumenamen ein, und klicken Sie aus **OK**.   

    ![Screenshot des Fensters „Sicherungen konfigurieren“ mit dem Richtlinienstatus „Anhalten“.](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Sicherungen eines Volumes löschen](backup-delete.md)
* [Metriken zur Volume-Sicherung](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)



