---
title: Häufig gestellte Fragen zu NFS für Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden häufig gestellte Fragen (FAQs) zum NFS-Protokoll in Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/19/2021
ms.openlocfilehash: e9c38f573a613debbb9f3e3c6b1da00ab23f92f1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270443"
---
# <a name="nfs-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zu NFS in Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zum NFS-Protokoll in Azure NetApp Files beantwortet.

## <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ich möchte, dass automatisch ein Volume eingebunden wird, wenn eine Azure-VM gestartet oder neu gestartet wird.  Wie konfiguriere ich meinen Host für persistente NFS-Volumes?

Damit beim Starten oder Neustarten einer VM automatisch ein NFS-Volume eingebunden wird, fügen Sie einen Eintrag für die Datei `/etc/fstab` zum Host hinzu. 

Weitere Informationen finden Sie unter [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  

## <a name="what-nfs-version-does-azure-netapp-files-support"></a>Welche NFS-Version wird von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt NFSv3 und NFSv4.1. Für die [Volumeerstellung](azure-netapp-files-create-volumes.md) können beide NFS-Versionen verwendet werden. 

## <a name="how-do-i-enable-root-squashing"></a>Wie aktiviere ich das Root Squashing?

Sie können angeben, ob für das Stammkonto (Root) Zugriff auf das Volume besteht, indem Sie die Exportrichtlinie des Volumes verwenden. Weitere Informationen finden Sie unter [Konfigurieren der Exportrichtlinie für ein NFS-Volume](azure-netapp-files-configure-export-policy.md).

## <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Kann ich den gleichen Dateipfad (Volumeerstellungstoken) für mehrere Volumes verwenden?

Ja, das ist möglich. Der Dateipfad muss jedoch innerhalb jedes Subnetzes eindeutig sein.     

## <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Warum nimmt der Client für das Durchsuchen von Ordnern und Unterordnern viel Zeit in Anspruch, wenn ich versuche, über einen Windows-Client auf NFS-Volumes zuzugreifen?

Stellen Sie sicher, dass `CaseSensitiveLookup` auf dem Windows-Client aktiviert ist, um die Suche nach Ordnern und Unterordnern zu beschleunigen:

1. Verwenden Sie den folgenden PowerShell-Befehl, um „CaseSensitiveLookup“ aktivieren:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Binden Sie das Volume auf dem Windows-Server ein.   
    Beispiel:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Wie unterstützt Azure NetApp Files die Dateisperrung von NFSv4.1? 

Für NFSv4.1-Clients unterstützt Azure NetApp Files den NFSv4.1-Mechanismus für die Dateisperrung, der den Status aller Dateisperren unter einem leasebasierten Modell aufrechterhält. 

Gemäß RFC 3530 definiert Azure NetApp Files eine einzelne Leasedauer für alle Zustände, die ein NFS-Client annimmt. Wenn der Client seine Leasedauer nicht innerhalb des definierten Zeitraums erneuert, werden alle Zustände, die mit der Leasedauer des Clients verbunden sind, vom Server freigegeben.  

Wenn z. B. ein Client, der ein Volume einbinden will, nicht mehr reagiert oder nach Ablauf des Timeouts abstürzt, werden die Sperren freigegeben. Der Client kann seine Leasedauer explizit oder implizit erneuern, indem er Vorgänge wie das Lesen einer Datei durchführt.   

Eine Toleranzperiode definiert einen Zeitraum für eine spezielle Verarbeitung, in dem Clients versuchen können, ihren Sperrstatus während einer Serverwiederherstellung freizugeben. Der Standardtimeout für die Leasedauer beträgt 30 Sekunden mit einer Toleranzperiode von 45 Sekunden. Nach dieser Zeit wird die Leasedauer des Clients freigegeben. 

## <a name="next-steps"></a>Nächste Schritte  

- [Häufig gestellte Fragen zu Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Häufig gestellte Fragen zu Microsoft Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Häufig gestellte Fragen zur Leistung von SMB für Azure NetApp Files](azure-netapp-files-smb-performance.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)