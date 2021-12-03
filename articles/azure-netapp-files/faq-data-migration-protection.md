---
title: Häufig gestellte Fragen zur Datenmigration und zum Schutz von Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden häufig gestellte Fragen (FAQs) zur Datenmigration und zum Schutz von Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c6422b99087b8239ea6d21c0f5186edd5df1c247
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270444"
---
# <a name="data-migration-and-protection-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zur Datenmigration und zum Schutz von Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Datenmigration und zum Schutz von Azure NetApp Files beantwortet.

## <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Wie migriere ich Daten zu Azure NetApp Files?
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Migrieren von Daten zum Dienst verwenden. 

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Datenmigration aus einer lokalen Umgebung nach Azure NetApp Files gelten folgende Anforderungen: 

- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen der Quelle und der IP-Adresse des Azure NetApp Files-Zielvolumes. Die Datenübertragung zwischen der lokalen Umgebung und dem Azure NetApp Files-Dienst wird über ExpressRoute unterstützt.
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

## <a name="where-does-azure-netapp-files-store-customer-data"></a>Wo speichert Azure NetApp Files Kundendaten?   

Standardmäßig verbleiben Ihre Daten in der Region, in der Sie Ihre Azure NetApp Files-Volumes bereitstellen. Sie können sich jedoch dafür entscheiden, Ihre Daten auf Volume-für-Volume-Basis in verfügbare Zielregionen zu replizieren, indem Sie [regionsübergreifende Replikation](cross-region-replication-introduction.md) verwenden.

## <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Wie erstelle ich eine Kopie eines Azure NetApp Files-Volumes in einer anderen Azure-Region?
    
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Replizieren von Daten zwischen Azure-Regionen verwenden. 

Mit der Funktion zur [regionsübergreifenden Replikation](cross-region-replication-introduction.md) können Sie Daten asynchron aus einem Azure NetApp Files-Volume (Quelle) in einer Region in ein anderes Azure NetApp Files-Volume (Ziel) in einer anderen Region replizieren.  Außerdem können Sie [unter Verwendung einer Momentaufnahme eines vorhandenen Volumes ein neues Volume erstellen](snapshots-restore-new-volume.md).

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Replikation eines Azure NetApp Files-Volumes in eine andere Azure-Region gelten folgende Anforderungen: 
- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen VNETs in jeder Region. Derzeit wird ein globales Peering zwischen VNETs nicht unterstützt.  Sie können Konnektivität zwischen VNETs herstellen, indem Sie eine Verbindung über eine ExpressRoute-Leitung erstellen oder eine S2S-VPN-Verbindung verwenden. 
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

## <a name="is-migration-with-azure-data-box-supported"></a>Wird die Migration mit Azure Data Box unterstützt?

Nein. Azure Data Box unterstützt Azure NetApp Files derzeit nicht. 

## <a name="is-migration-with-azure-importexport-service-supported"></a>Wird die Migration mit dem Azure Import/Export-Dienst unterstützt?

Nein. Der Azure Import/Export-Dienst unterstützt Azure NetApp Files derzeit nicht.

## <a name="next-steps"></a>Nächste Schritte  

- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)
