---
title: Deaktivieren der Sicherungsfunktionalität für ein Azure NetApp Files-Volume | Microsoft-Dokumentation
description: Beschreibt, wie Sie die Sicherungsfunktionalität für ein Volume deaktivieren, das keinen Sicherungsschutz mehr benötigt.
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
ms.openlocfilehash: 1555a1c42f6365202787750f149b345ccd0cd55c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224141"
---
# <a name="disable-backup-functionality-for-a-volume"></a>Deaktivieren der Sicherungsfunktionalität für ein Volume 

Sie können die Sicherungsfunktionalität für ein Volume deaktivieren, wenn Sie den Sicherungsschutz nicht mehr benötigen. 

> [!IMPORTANT]
> Wenn Sie Sicherungen für ein Volume deaktivieren, werden alle Sicherungen gelöscht, die im Azure-Speicher für dieses Volume gespeichert sind.

Wenn ein Volume gelöscht wird, die Sicherungsrichtlinie vor dem Löschen des Volumes aber nicht deaktiviert wurde, bleiben alle Sicherungen im Zusammenhang mit dem Volume im Azure-Speicher erhalten und werden unter dem zugeordneten NetApp-Konto aufgeführt. 

## <a name="steps"></a>Schritte

1. Wählen Sie **Volumes** aus.
2. Wählen Sie das spezifische Volume aus, dessen Sicherungsfunktionalität Sie deaktivieren möchten.
3. Wählen Sie **Konfigurieren** aus.
4. Schalten Sie auf der Seite „Sicherungen konfigurieren“ die Einstellung **Aktiviert** auf **Aus** um. Geben Sie den zu bestätigenden Volumenamen ein, und klicken Sie auf **OK**.

    ![Screenshot: Fenster „Wiederherstellen in“ mit Fenster „Sicherungen konfigurieren“ mit deaktivierter Sicherung.](../media/azure-netapp-files/backup-configure-backups-disable.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Löschen von Sicherungen eines Volumes](backup-delete.md)
* [Metriken zur Volume-Sicherung](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)

