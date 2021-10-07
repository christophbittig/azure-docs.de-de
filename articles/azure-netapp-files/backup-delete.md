---
title: Löschen von Sicherungen eines Azure NetApp Files-Volumes | Microsoft-Dokumentation
description: Beschreibt, wie Sie einzelne Sicherungen löschen, die Sie für ein Volume nicht mehr aufbewahren müssen.
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
ms.openlocfilehash: c63fc37000fbd6cf376dd5a80b392e87466e9ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094983"
---
# <a name="delete-backups-of-a-volume"></a>Löschen von Sicherungen eines Volumes 

Sie können einzelne Sicherungen löschen, die Sie für ein Volume nicht mehr aufbewahren müssen. Durch das Löschen von Sicherungen werden die zugeordneten Objekte in Ihrem Azure Storage-Konto gelöscht, wodurch Speicherplatz gespart wird.  

Sie können die letzte Sicherung nicht löschen.  

## <a name="steps"></a>Schritte

1. Wählen Sie **Volumes** aus.
2. Navigieren Sie zu **Sicherungen**.
3. Wählen Sie in der Sicherungsliste die zu löschende Sicherung aus. Klicken Sie rechts neben der Sicherung auf die drei Punkte (`…`), und klicken Sie dann im Menü „Aktion“ auf **Löschen**.

    ![Screenshot des Menüs „Löschen“ für Sicherungen.](../media/azure-netapp-files/backup-action-menu-delete.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Volumesicherungsmetriken](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
