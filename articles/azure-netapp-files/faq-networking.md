---
title: Häufig gestellte Fragen zum Netzwerkbetrieb von Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel werden häufig gestellte Fragen (FAQs) zum Netzwerkbetrieb von Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 11/08/2021
ms.openlocfilehash: 9539cf9cc327cb954a852c183cf8c94ee4d56ca1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054788"
---
# <a name="networking-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zum Netzwerkbetrieb von Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zum Netzwerkbetrieb von Azure NetApp Files beantwortet. 

## <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Wird der Datenpfad für NFS oder SMB über das Internet geleitet?  

Nein. Der Datenpfad für NFS oder SMB wird nicht über das Internet geleitet. Azure NetApp Files ist ein nativer Azure-Dienst, der in dem Azure Virtual Network (VNET) bereitgestellt wird, in dem der Dienst verfügbar ist. Azure NetApp Files verwendet ein delegiertes Subnetz und stellt eine Netzwerkschnittstelle direkt im VNET bereit. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).  

## <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kann ich eine Verbindung zu einem VNET herstellen, das ich für den Azure NetApp Files-Dienst bereits erstellt habe?

Ja, Sie können VNETs verbinden, die Sie für den Dienst erstellt haben. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).  

## <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kann ich ein NFS-Volume von Azure NetApp Files anhand des vollqualifizierten DNS-Domänennamens einbinden?

Ja, wenn Sie die erforderlichen DNS-Einträge erstellen. Azure NetApp Files liefert die Dienst-IP-Adresse für das bereitgestellte Volume. 

> [!NOTE] 
> Azure NetApp Files kann bei Bedarf weitere IP-Adressen für den Dienst bereitstellen.  DNS-Einträge müssen möglicherweise in regelmäßigen Abständen aktualisiert werden.

## <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kann ich meine eigene IP-Adresse für ein Azure NetApp Files-Volume festlegen oder auswählen?  

Nein. Die IP-Zuweisung zu Azure NetApp Files-Volumes ist dynamisch. Die statische IP-Zuweisung wird nicht unterstützt. 

## <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Unterstützt Azure NetApp Files Doppelstapel-VNet (IPv4 und IPv6)?

Nein, Azure NetApp Files unterstützt derzeit nicht die Bereitstellung von Volumes in einem delegierten Subnetz mit dualem Stapel (IPv4 und IPv6). Das delegierte Subnetz für den Azure NetApp Files-Dienst muss ein reines IPv4-Subnetz sein. Der Zugriff auf Azure NetApp Files per IPv4 ist jedoch über ein Subnetz mit dualem Stapel oder per (mittels Peering verknüpftem) VNet möglich.

## <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>Ist die Anzahl der IP-Adressen, die Azure VMware Solution für Gastbetriebssystemeinbindungen verwenden, auf 1.000 beschränkt?

Nein. Azure VMware Solution befindet sich hinter einem ER-Gateway. Dadurch verhält sich der Dienst ähnlich wie ein lokales System. Die Anzahl der AVS-„Hosts“ und -„Gäste“ ist für Azure NetApp Files nicht sichtbar, und der [Grenzwert von 1000 IP-Adressen](azure-netapp-files-resource-limits.md#resource-limits) ist nicht anwendbar.
 
## <a name="next-steps"></a>Nächste Schritte  

- [Häufig gestellte Fragen zu Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Häufig gestellte Fragen zu Microsoft Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)
