---
title: Häufig gestellte Fragen zur Integration bei Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden häufig gestellte Fragen (FAQs) zur Verwendung anderer Produkte oder Dienste mit Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c48a35b4578488fdab5fc55cdca4e19d97a003d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270551"
---
# <a name="integration-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zur Integration bei Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Verwendung anderer Produkte oder Dienste mit Azure NetApp Files beantwortet.  

## <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Kann ich NFS- oder SMB-Volumes von Azure NetApp Files mit Azure VMware Solution (AVS) verwenden?

Sie können NFS-Volumes von Azure NetApp Files auf Windows-VMs oder Linux-VMs von AVS einbinden. Sie können SMB-Freigaben von Azure NetApp Files auf Windows-VMs von AVS zuordnen. Weitere Informationen finden Sie unter [Azure NetApp Files mit Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

## <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Welche Regionen werden für die Verwendung von NFS- oder SMB-Volumes von Azure NetApp Files mit Azure VMware Solution (AVS) unterstützt?

Die Nutzung von Azure NetApp Files-NFS- oder -SMB-Volumes mit AVS für *Gastbetriebssystem-Einbindungen* wird in [allen AVS- and ANF-fähigen Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp) unterstützt.

## <a name="does-azure-netapp-files-work-with-azure-policy"></a>Kann Azure NetApp Files mit Azure Policy verwendet werden?

Ja. Azure NetApp Files ist ein Erstanbieterdienst. Er entspricht vollständig den Standards für Azure-Ressourcenanbieter. Somit kann Azure NetApp Files über *benutzerdefinierte Richtliniendefinitionen* in Azure Policy integriert werden. Informationen zum Implementieren von benutzerdefinierten Richtlinien für Azure NetApp Files finden Sie in der Microsoft Tech Community unter [Azure Policy now available for Azure NetApp Files](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258) (Azure Policy jetzt für Azure NetApp Files verfügbar). 

## <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>Welche Unicode-Zeichencodierung wird von Azure NetApp Files für das Erstellen und Anzeigen von Datei- und Verzeichnisnamen unterstützt?   

Azure NetApp Files unterstützt nur Datei- und Verzeichnisnamen, die mit der UTF-8-Zeichencodierung für NFS- und SMB-Volumes codiert sind.

Wenn Sie versuchen, Dateien oder Verzeichnisse mit Namen zu erstellen, die ergänzende Zeichen oder Ersatzzeichenpaare verwenden (z. B. nicht reguläre Zeichen oder Emojis, die von UTF-8 nicht unterstützt werden), dann schlägt der Vorgang fehl. Eine Fehlermeldung des Windows-Client lautet in diesem Fall z. B.: „Der angegebene Dateiname ist ungültig oder zu lang. Geben Sie einen anderen Dateinamen an.” 

## <a name="next-steps"></a>Nächste Schritte  

- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
