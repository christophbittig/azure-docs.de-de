---
title: Grundlegendes zur Azure NetApp Files-Sicherung | Microsoft-Dokumentation
description: Beschreibt, was die Azure NetApp Files-Sicherung macht, unterstützte Regionen sowie das Kostenmodell.
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
ms.topic: conceptual
ms.date: 10/13/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 81d14b6c03e8ef27697e8f069a5d9ebd5ff38d7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993129"
---
# <a name="understand-azure-netapp-files-backup"></a>Grundlegendes zur Azure NetApp Files-Sicherung

Die Azure NetApp Files-Sicherung erweitert die Datenschutzfunktionen von Azure NetApp Files durch die Bereitstellung einer vollständig verwalteten Sicherungslösung für die langfristige Wiederherstellung, Archivierung und Compliance. Vom Dienst erstellte Sicherungen werden unabhängig von Volumemomentaufnahmen, die für die kurzfristige Wiederherstellung oder das Klonen verfügbar sind, in Azure Storage gespeichert. Vom Dienst erstellte Sicherungen können auf neuen Azure NetApp Files-Volumes innerhalb der Region wiederhergestellt werden. Die Azure NetApp Files-Sicherung unterstützt sowohl richtlinienbasierte (geplante) als auch manuelle (bedarfsbasierte) Sicherungen. Weitere Informationen finden Sie unter [Funktionsweise Azure NetApp Files-Momentaufnahmen](snapshots-introduction.md).

> [!IMPORTANT]
> Das Azure NetApp Files-Sicherungsfeature befindet sich derzeit in der Vorschauphase. Sie müssen eine Wartelistenanforderung für den Zugriff auf das Feature über die Seite für **[Public Preview der Azure NetApp Files-Sicherung](https://aka.ms/anfbackuppreviewsignup)** übermitteln. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie das Azure NetApp Files-Sicherungsfeature verwenden.

## <a name="supported-regions"></a>Unterstützte Regionen 

Azure NetApp Files-Sicherung wird für die folgenden Regionen unterstützt:   

* East US
* USA (Ost) 2
* USA (Westen) 
* Mitte/Süd
* USA, Westen 2
* Nordeuropa 
* Europa, Westen
* Australien (Osten)
* Japan, Osten

## <a name="cost-model-for-azure-netapp-files-backup"></a>Kostenmodell für Azure NetApp Files-Sicherung

Die Preise für die Azure NetApp Files-Sicherung basieren auf der Gesamtmenge des von der Sicherung verbrauchten Speichers. Es fallen keine Einrichtungsgebühren oder Mindestnutzungsgebühren an. Die Kosten für die Sicherungswiederherstellung basieren auf der Gesamtmenge der während des Abrechnungszyklus wiederhergestellten Sicherungskapazität.

Gehen Sie als Preisbeispiel von den folgenden Situationen aus:

* Ihr Quellvolume stammt aus der Premium-Dienstebene von Azure NetApp Files. Die Größe des Volumekontingents beträgt 1000 GiB und die vom Volume genutzte Größe beträgt 500 GiB zu Beginn des ersten Tages eines Monats. Das Volume befindet sich in der Region USA, Süden-Mitte.
* Sie haben eine tägliche *Momentaufnahme* richtlinie mit fünf zu behaltenden lokalen Momentaufnahmen und eine tägliche *Sicherungs* richtlinie zum Behalten von 30 Sicherungskopien konfiguriert.
* Gehen Sie aus Gründen der Einfachheit davon aus, dass es auf Ihrem Quellvolume konstant zu einer täglichen Datenänderung von 1 %kommt, sich die insgesamt verbrauchte Größe des Volumes aber nicht erhöht (sie bleibt bei 500 GiB).

Wenn die Sicherungsrichtlinie dem Volume zugewiesen wird, wird die Baselinesicherung im vom Dienst verwalteten Azure-Speicher initiiert. Nach Abschluss der Sicherung wird die Baselinesicherung von 500 GiB zur Sicherungsliste des Volumes hinzugefügt. Nach der Baselineübertragung sichern tägliche Sicherungen nur geänderte Blöcke. Angenommen, es werden täglich inkrementelle Sicherungen von 5 GiB hinzugefügt, dann betrüge der gesamte verbrauchte Sicherungsspeicher `500GiB + 30*5GiB = 650GiB`.

Die Sicherung wird Ihnen am Ende des Monats mit einer Gebühr von 0,05 USD pro Monat für die Gesamtmenge des von der Sicherung verbrauchten Speichers berechnet.  Das heißt 650 GiB mit einer monatlichen Gesamtsicherungsgebühr von `650*$0.05=$32.5`. Die reguläre Azure NetApp Files-Speicherkapazität gilt für lokale Momentaufnahmen. Weitere Informationen finden Sie auf der Seite für die [Azure NetApp Files-Preise](https://azure.microsoft.com/pricing/details/netapp/).

Wenn Sie eine Sicherung von beispielsweise 600 GiB auf einem neuen Volume wiederherstellen möchten, werden Ihnen 0,02 USD pro GiB wiederhergestellter Sicherungskapazität in Rechnung gestellt. In diesem Fall ist dies `600*$0.02 = $12` für den Wiederherstellungsvorgang. 

## <a name="next-steps"></a>Nächste Schritte

* [Anforderungen und Überlegungen für Azure NetApp Files](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Sicherungen eines Volumes löschen](backup-delete.md)
* [Metriken zur Volume-Sicherung](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Funktionsweise von Azure NetApp Files-Momentaufnahmen](snapshots-introduction.md)
