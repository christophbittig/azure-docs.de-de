---
title: Häufig gestellte Fragen zur Leistung bei Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden häufig gestellte Fragen (FAQs) zur Leistung von Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: e7b489a2f9960b39fcb8035889b47468c2af0478
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270571"
---
# <a name="performance-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zur Leistung von Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Leistung von Azure NetApp Files beantwortet.

## <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Was kann ich tun, um die Leistung von Azure NetApp Files zu optimieren?

Je nach Leistungsanforderungen können Sie folgende Aktionen ausführen: 
- Stellen Sie sicher, dass die VM eine geeignete Größe aufweist.
- Aktivieren Sie den beschleunigten Netzwerkbetrieb für die VM.
- Wählen Sie die gewünschte Dienstebene und -größe für den Kapazitätspool.
- Erstellen Sie ein Volume mit der gewünschten Kontingentgröße für Kapazität und Leistung.

## <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Wie konvertiere ich durchsatzbasierte Dienstebenen von Azure NetApp Files zu IOPS?

Sie können MB/s mit der folgenden Formel zu IOPS konvertieren:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

## <a name="how-do-i-change-the-service-level-of-a-volume"></a>Wie ändere ich die Dienstebene eines Volumes?

Sie können den Servicelevel eines vorhandenen Volumes ändern, indem Sie das Volume in einen anderen Kapazitätspool verschieben, der den von Ihnen für das Volume gewünschten [Servicelevel](azure-netapp-files-service-levels.md) verwendet. Siehe [Dynamisches Ändern der Dienstebene eines Volumes](dynamic-change-volume-service-level.md). 

## <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Wie überwache ich die Leistung von Azure NetApp Files?

Azure NetApp Files bietet Metriken zum Messen der Volumeleistung. Sie können auch Azure Monitor verwenden, um die Nutzungsmetriken für Azure NetApp Files zu überwachen.  Eine Liste der Leistungsmetriken für Azure NetApp Files finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md).

## <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Was sind die Leistungsauswirkungen von Kerberos auf NFSv4.1?

Informationen zu den Sicherheitsoptionen für NFSv4.1, den getesteten Leistungsvektoren und den erwarteten Leistungsauswirkungen finden Sie unter [Leistungsauswirkungen von Kerberos auf NFSv4.1-Volumes](performance-impact-kerberos.md). 

## <a name="does-azure-netapp-files-support-smb-direct"></a>Unterstützt Azure NetApp Files SMB Direct?

Nein, Azure NetApp Files unterstützt SMB Direct nicht. 

## <a name="is-nic-teaming-supported-in-azure"></a>Wird NIC-Teaming von Azure unterstützt?

NIC-Teaming wird von Azure nicht unterstützt. Auch wenn auf virtuellen Azure-Computern mehrere Netzwerkschnittstellen unterstützt werden, stellen sie eher ein logisches als ein physisches Konstrukt dar. Daher bieten sie keine Fehlertoleranz.  Außerdem wird die Bandbreite, die für einen virtuellen Azure-Computer verfügbar ist, für den Computer selbst und nicht für einzelne Netzwerkschnittstellen berechnet.

## <a name="are-jumbo-frames-supported"></a>Werden Großrahmen unterstützt?

Großrahmen werden für virtuelle Azure-Computer nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte  

- [Auswirkungen von Kerberos auf die Leistung von NFSv4.1-Volumes](performance-impact-kerberos.md)
- [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md    )
- [Testempfehlungen von Leistungsbenchmarks für Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md )
- [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md)
- [Auswirkungen von Kerberos auf die Leistung von NFSv4.1-Volumes](performance-impact-kerberos.md)
- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)