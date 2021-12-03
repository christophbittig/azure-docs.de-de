---
title: Häufig gestellte Fragen zu SMB für Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden häufig gestellte Fragen (FAQs) zum SMB-Protokoll in Azure NetApp Files beantwortet.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 7b1c6b1e3e9814ed83c92ada2388ff54f5b3829c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270610"
---
# <a name="smb-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zu SMB in Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zum SMB-Protokoll in Azure NetApp Files beantwortet.

## <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Welche SMB-Versionen werden von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt SMB 2.1 und SMB 3.1 (Unterstützung für SMB 3.0 ist hierbei eingeschlossen). 

## <a name="is-an-active-directory-connection-required-for-smb-access"></a>Ist für den SMB-Zugriff eine Active Directory-Verbindung erforderlich? 

Ja, Sie müssen eine Active Directory-Verbindung erstellen, bevor Sie ein SMB-Volume bereitstellen. Damit erfolgreich eine Verbindung hergestellt werden kann, muss das delegierte Subnetz von Azure NetApp Files auf die angegebenen Domänencontroller zugreifen können.  Ausführliche Informationen dazu finden Sie unter [Erstellen eines SMB-Volumes](./azure-netapp-files-create-volumes-smb.md). 

## <a name="how-many-active-directory-connections-are-supported"></a>Wie viele Active Directory-Verbindungen werden unterstützt?

Sie können nur eine Active Directory-Verbindung (AD) pro Abonnement und pro Region konfigurieren. Weitere Informationen finden Sie unter [Anforderungen für Active Directory-Verbindungen](create-active-directory-connections.md#requirements-for-active-directory-connections). 

Sie können jedoch mehrere NetApp-Konten, die sich unter demselben Abonnement und derselben Region befinden, einem allgemeinen AD-Server zuordnen, der in einem der NetApp-Konten erstellt wurde. Weitere Informationen finden Sie unter [Zuordnen mehrerer NetApp-Konten im selben Abonnement und derselben Region zu einer AD-Verbindung](create-active-directory-connections.md#shared_ad). 

## <a name="does-azure-netapp-files-support-azure-active-directory"></a>Unterstützt Azure NetApp Files Azure Active Directory? 

Sowohl [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) als auch [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) wird unterstützt. Bereits vorhandene Active Directory-Domänencontroller können mit Azure NetApp Files verwendet werden. Domänencontroller können als virtuelle Computer in Azure oder lokal über ExpressRoute oder S2S-VPN betrieben werden. Azure NetApp Files unterstützt aktuell keine AD-Einbindung für [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/).

Bei Verwendung von Azure NetApp Files mit Azure Active Directory Domain Services lautet der Pfad der Organisationseinheit `OU=AADDC Computers`, wenn Sie Active Directory für Ihr NetApp-Konto konfigurieren.

## <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Welche Versionen von Windows Server Active Directory werden unterstützt?

Azure NetApp Files unterstützt die Windows Server-Versionen 2008 R2 SP1 – 2019 von Active Directory Domain Services.

## <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Ich habe Probleme beim Herstellen einer Verbindung mit meiner SMB-Freigabe.   Wie sollte ich vorgehen?

Legen Sie als bewährte Vorgehensweise die maximale Toleranz für die Synchronisierung der Computeruhr auf fünf Minuten fest. Weite Informationen finden Sie unter [Maximale Toleranz für die Synchronisierung der Computeruhr](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

## <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Kann ich `SMB Shares`, `Sessions` und `Open Files` über die Computerverwaltung (MMC) verwalten?

Die Verwaltung von `SMB Shares`, `Sessions` und `Open Files` über die Computerverwaltung (MMC) wird derzeit nicht unterstützt.

## <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Wie kann ich die IP-Adresse eines SMB-Volumes über das Portal abrufen?

Verwenden Sie den Link **JSON-Ansicht** im Bereich mit der Volumeübersicht, und suchen Sie unter **properties** -> **mountTargets** nach dem Bezeichner **startIp**.

## <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Kann eine Azure NetApp Files-SMB-Freigabe als Stamm für den DFS-Namespace (DFS-N) fungieren?

Nein. Azure NetApp Files-SMB-Freigaben können jedoch als Ordnerziel für den DFS-Namespace (DFS-N) dienen.   
Um eine Azure NetApp Files-SMB-Freigabe als DFS-N-Ordnerziel zu verwenden, geben Sie den UNC-Einbindungspfad (Universal Naming Convention) der Azure NetApp Files-SMB-Freigabe entsprechend den Schritten zum [Hinzufügen von Ordnerzielen](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target) an.  

## <a name="can-the-smb-share-permissions-be-changed"></a>Können die SMB-Freigabeberechtigungen geändert werden?   

Nein, die Freigabeberechtigungen können nicht geändert werden. Die NTFS-Berechtigungen des `root`-Volumes können jedoch mithilfe des [NTFS-Datei- und -Ordnerberechtigungsverfahrens](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions) geändert werden. 


## <a name="next-steps"></a>Nächste Schritte  

- [Häufig gestellte Fragen zur Leistung von SMB für Azure NetApp Files](azure-netapp-files-smb-performance.md)
- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)
