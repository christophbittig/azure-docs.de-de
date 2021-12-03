---
title: Häufig gestellte Fragen zur Sicherheit von Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Sicherheit von Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.custom: references_regions
ms.openlocfilehash: d1794516cfd6c97411d27122749a4260bcbe8358
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270611"
---
# <a name="security-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zur Sicherheit von Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen zur Sicherheit von Azure NetApp Files beantwortet.

## <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kann der Netzwerkdatenverkehr zwischen Azure-VM und Speicher verschlüsselt werden?

Azure NetApp Files-Datenverkehr ist entwurfsbedingt grundsätzlich sicher, da er keinen öffentlichen Endpunkt bereitstellt und der Datenverkehr innerhalb des kundeneigenen VNet verbleibt. Daten während der Übertragung werden nicht standardmäßig verschlüsselt. Der Datenverkehr von einer Azure-VM (auf der ein NFS- oder SMB-Client ausgeführt wird) zu Azure NetApp Files ist jedoch so sicher wie jeder andere Datenverkehr zwischen Azure-VMs. 

Das NFSv3-Protokoll bietet keine Unterstützung für die Verschlüsselung, sodass diese Daten während der Übertragung nicht verschlüsselt werden können. NFSv4.1- und SMB3-Verschlüsselung von Daten während der Übertragung kann jedoch optional aktiviert werden. Der Datenverkehr zwischen NFSv4.1-Clients und Azure NetApp Files-Volumes kann mit Kerberos per AES-256-Verschlüsselung verschlüsselt werden. Weitere Informationen finden Sie unter [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files](configure-kerberos-encryption.md). Der Datenverkehr zwischen SMB3-Clients und Azure NetApp Files-Volumes kann mithilfe des AES-CCM-Algorithmus für SMB 3.0- und mit dem AES-GCM-Algorithmus für SMB 3.1.1-Verbindungen verschlüsselt werden. Ausführliche Informationen finden Sie unter [Erstellen eines SMB-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="can-the-storage-be-encrypted-at-rest"></a>Kann der Speicher im Ruhezustand verschlüsselt werden?

Alle Azure NetApp Files-Volumes werden mit dem FIPS 140-2-Standard verschlüsselt. Alle Schlüssel werden vom Azure NetApp Files-Dienst verwaltet. 

## <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Wird der Datenverkehr bei der regionsübergreifenden Replikation in Azure NetApp Files verschlüsselt?

Alle zwischen dem Quellvolume und dem Zielvolume übertragenen Daten werden bei der regionsübergreifenden Replikation in Azure NetApp Files per TLS 1.2 AES-256 GCM-Verschlüsselung verschlüsselt. Diese Verschlüsselung erfolgt zusätzlich zur [Azure MACSec-Verschlüsselung](../security/fundamentals/encryption-overview.md), die standardmäßig für den gesamten Azure-Datenverkehr aktiviert ist, auch für den Datenverkehr bei der regionsübergreifenden Replikation in Azure NetApp Files. 

## <a name="how-are-encryption-keys-managed"></a>Wie werden Verschlüsselungsschlüssel verwaltet? 

Die Schlüsselverwaltung für Azure NetApp Files wird vom Dienst verarbeitet. Für jedes Volume wird ein eindeutiger XTS-AES-256-Datenverschlüsselungsschlüssel generiert. Zum Verschlüsseln und Schützen aller Volumeschlüssel wird eine Verschlüsselungsschlüsselhierarchie verwendet. Diese Verschlüsselungsschlüssel werden niemals in unverschlüsseltem Format angezeigt oder gemeldet. Verschlüsselungsschlüssel werden sofort gelöscht, wenn ein Volume gelöscht wird.

Unterstützung von kundenseitig verwalteten Schlüsseln (Bring Your Own Key) mithilfe von Azure Dedicated HSM ist in den Regionen „USA, Osten“, „USA, Süden-Mitte“, „USA, Westen2“ und „US Gov Virginia“ unter Kontrolle verfügbar. Sie können den Zugriff unter [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) anfordern. Entsprechend der zunehmenden Verfügbarkeit von Kapazitäten werden Anforderungen genehmigt.

## <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kann ich Regeln für NFS-Exportrichtlinien konfigurieren, um den Zugriff auf das Azure NetApp Files-Diensteinbindungsziel zu steuern?

Ja, Sie können bis zu fünf Regeln in einer einzigen NFS-Exportrichtlinie konfigurieren.

## <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Kann ich Azure RBAC mit Azure NetApp Files verwenden?

Ja, Azure NetApp Files unterstützt Azure RBAC-Features. Neben den integrierten Azure-Rollen können [benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md) für Azure NetApp Files erstellt werden. 

Eine vollständige Liste der Azure NetApp Files-Berechtigungen finden Sie in den Azure-Ressourcenanbietervorgängen für [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp).

## <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Werden Azure-Aktivitätsprotokolle für Azure NetApp Files unterstützt?

Bei Azure NetApp Files handelt es sich um einen nativen Azure-Dienst. All PUT-, POST- und DELETE-APIs für Azure NetApp Files werden protokolliert. Die Protokolle geben beispielsweise Aufschluss darüber, wer die Momentaufnahme erstellt oder das Volume geändert hat.

Eine vollständige Liste der API-Vorgänge finden Sie unter [Azure NetApp Files-REST-API](/rest/api/netapp/).

## <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Kann ich Azure-Richtlinien mit Azure NetApp Files verwenden?

Ja. Sie können [benutzerdefinierte Azure-Richtlinien](../governance/policy/tutorials/create-custom-policy-definition.md) erstellen. 

Sie können allerdings keine Azure-Richtlinien (benutzerdefinierte Benennungsrichtlinien) für die Azure NetApp Files-Schnittstelle erstellen. Lesen Sie den Artikel mit den [Richtlinien für die Azure NetApp Files-Netzwerkplanung](azure-netapp-files-network-topologies.md#considerations).

## <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Werden die Daten sicher gelöscht, wenn ich ein Azure NetApp Files-Volume lösche? 

Das Löschen eines Azure NetApp Files-Volumes erfolgt programmgesteuert und mit sofortiger Wirkung. Der Löschvorgang umfasst das Löschen von Schlüsseln, die zum Verschlüsseln der ruhenden Daten verwendet werden. Es gibt keine Möglichkeit, ein gelöschtes Volume wiederherzustellen, nachdem der Löschvorgang erfolgreich ausgeführt wurde (über Schnittstellen wie das Azure-Portal und die API).


## <a name="next-steps"></a>Nächste Schritte  

- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)