---
title: Häufig gestellte Fragen zu Azure NetApp Files | Microsoft-Dokumentation
description: Beantwortet häufig gestellte Fragen (FAQs) zur Kapazitätsverwaltung in Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 1f28b7b590ef2a7a3894f9eff7eff7199167afc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270589"
---
# <a name="capacity-management-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zur Kapazitätsverwaltung in Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Kapazitätsverwaltung in Azure NetApp Files beantwortet.

## <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Wie überwache ich die Nutzung für den Kapazitätspool und das Volume von Azure NetApp Files? 

Azure NetApp Files stellt Nutzungsmetriken für Kapazitätspool und Volume bereit. Sie können auch Azure Monitor verwenden, um die Nutzung für Azure NetApp Files zu überwachen. Ausführliche Informationen finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md). 

## <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Wie bestimme ich, ob ein Verzeichnis dabei ist, sein Größenlimit zu erreichen?

Sie können den Befehl `stat` von einem Client aus verwenden, um festzustellen, ob sich ein Verzeichnis der [maximal zulässigen Größe](azure-netapp-files-resource-limits.md#resource-limits) für Verzeichnismetadaten (320 MB) nähert.
Informationen zum Grenzwert und zur Berechnung finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md#directory-limit). 

## <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>Zählt der Speicherplatz für Momentaufnahmen zur nutzbaren oder bereitgestellten Kapazität eines Volumes?

Ja, die für Momentaufnahmen [genutzte Kapazität](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) zählt zum bereitgestellten Speicherplatz im Volume. Wenn das Volume vollständig ausgelastet ist, kommen folgende Maßnahmen in Frage:

* [Ändern Sie die Größe des Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md).
* [Entfernen Sie ältere Momentaufnahmen](snapshots-delete.md), um Speicherplatz auf dem Hostingvolume freizugeben. 

## <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Wird in Azure NetApp Files die automatische Vergrößerung für Volumes oder Kapazitätspools unterstützt?

Nein, Volumes und Kapazitätspools werden in Azure NetApp Files beim Auffüllen nicht automatisch vergrößert. Weitere Informationen finden Sie unter [Kostenmodell für Azure NetApp Files](azure-netapp-files-cost-model.md).   

Sie können das von der Community unterstützte [Logic Apps-Tool ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager) verwenden, um kapazitätsbasierte Warnungsregeln zu verwalten. Das Tool kann die Volumegröße automatisch erhöhen, um zu verhindern, dass auf Ihren Volumes kein Speicherplatz mehr verfügbar ist.

## <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>Wird das Zielvolume einer Replikation auf das feste Volumekontingent angerechnet?  

Nein, das Zielvolume einer Replikation wird nicht auf das feste Volumekontingent angerechnet.

## <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kann ich Azure NetApp Files über den Azure Storage-Explorer verwalten?

Nein. Azure NetApp Files wird vom Azure Storage-Explorer nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte  

- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)
