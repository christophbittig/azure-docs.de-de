---
title: Wiederherstellen einer Sicherung auf einem neuen Azure NetApp Files-Volume | Microsoft-Dokumentation
description: Beschreibt, wie eine Sicherung auf einem neuen Volume wiederhergestellt wird.
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
ms.openlocfilehash: 1a0b2e59f2d11caf1db9985cfba937e2df0f44c7
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095007"
---
# <a name="restore-a-backup-to-a-new-volume"></a>Wiederherstellen einer Sicherung auf einem neuen Volume

Beim Wiederherstellen einer Sicherung wird ein neues Volume mit demselben Protokolltyp erstellt. In diesem Artikel wird der Wiederherstellungsvorgang erläutert. 

## <a name="considerations"></a>Überlegungen

* Das durch den Wiederherstellungsvorgang erstellte neue Volume kann erst nach Abschluss der Wiederherstellung eingebunden werden. 

* Sie sollten den Wiederherstellungsvorgang auslösen, wenn keine Baselinesicherungen vorhanden sind. Andernfalls kann die Wiederherstellung die Last für das Azure Blob-Konto erhöhen, in dem Ihre Daten gesichert werden. 

## <a name="steps"></a>Schritte

1. Wählen Sie **Volumes** aus. Navigieren Sie zu **Sicherungen**.

    > [!NOTE]
    > Wenn ein Volume gelöscht wird, die Sicherungsrichtlinie vor dem Löschen des Volumes aber nicht deaktiviert wurde, bleiben alle Sicherungen im Zusammenhang mit dem Volume im Azure-Speicher erhalten und sind unter dem zugeordneten NetApp-Konto zu finden.  Siehe [Suchen von Sicherungen auf NetApp-Kontoebene](backup-search.md#search-backups-at-netapp-account-level).


2. Wählen Sie in der Sicherungsliste die wiederherzustellende Sicherung aus. Klicken Sie rechts neben der Sicherung auf die drei Punkte (`…`), und klicken Sie dann im Menü „Aktion“ auf **Auf neuem Volume wiederherstellen**.   

    ![Screenshot der Option zum Wiederherstellen der Sicherung auf einem neuen Volume.](../media/azure-netapp-files/backup-restore-new-volume.png)

3. Geben Sie auf der angezeigten Seite „Volume erstellen“ geeignete Informationen für die Felder auf der Seite an, und klicken Sie auf **Überprüfen + erstellen**, um mit der Wiederherstellung der Sicherung auf einem neuen Volume zu beginnen.   

    * Das Feld **Protokoll** wird vorab vom ursprünglichen Volume aufgefüllt und kann nicht geändert werden.    
        Wenn Sie jedoch ein Volume aus der Sicherungsliste auf NetApp-Kontoebene wiederherstellen, müssen Sie das Feld „Protokoll“ angeben. Das Feld „Protokoll“ muss mit dem Protokoll des ursprünglichen Volumes übereinstimmen. Andernfalls schlägt der Wiederherstellungsvorgang mit dem folgenden Fehler fehl:  
        `Protocol Type value mismatch between input and source volume of backupId <backup-id of the selected backup>. Supported protocol type : <Protocol Type of the source volume>`

    * Der **Kontingent** wert muss größer als oder gleich der Größe der Sicherung sein, aus der die Wiederherstellung ausgelöst wird (mindestens 100 GiB).

    * Der **Kapazitätspool**, in dem die Sicherung wiederhergestellt wird, muss über ausreichende ungenutzte Kapazität zum Hosten des neuen wiederhergestellten Volumes verfügen. Andernfalls schlägt der Wiederherstellungsvorgang fehl.   

    ![Screenshot der Seite „Volume erstellen“.](../media/azure-netapp-files/backup-restore-create-volume.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Löschen von Sicherungen eines Volumes](backup-delete.md)
* [Volumesicherungsmetriken](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



