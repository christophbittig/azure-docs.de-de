---
title: Häufig gestellte Fragen zu Azure NetApp Files | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu Azure NetApp Files, z. B. Netzwerk, Sicherheit, Leistung, Kapazitätsverwaltung und Datenmigration und -schutz.
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
ms.date: 09/01/2021
ms.author: b-juche
ms.openlocfilehash: 119cf21f90102f7ebccd8e4e06cd5e5dee3c4bfe
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427982"
---
# <a name="faqs-about-azure-netapp-files"></a>Häufig gestellte Fragen zu Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen zu Azure NetApp Files beantwortet. 

## <a name="networking-faqs"></a>Häufig gestellte Fragen zum Netzwerk

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Wird der Datenpfad für NFS oder SMB über das Internet geleitet?  

Nein. Der Datenpfad für NFS oder SMB wird nicht über das Internet geleitet. Azure NetApp Files ist ein nativer Azure-Dienst, der in dem Azure Virtual Network (VNET) bereitgestellt wird, in dem der Dienst verfügbar ist. Azure NetApp Files verwendet ein delegiertes Subnetz und stellt eine Netzwerkschnittstelle direkt im VNET bereit. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kann ich eine Verbindung zu einem VNET herstellen, das ich für den Azure NetApp Files-Dienst bereits erstellt habe?

Ja, Sie können VNETs verbinden, die Sie für den Dienst erstellt haben. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kann ich ein NFS-Volume von Azure NetApp Files anhand des vollqualifizierten DNS-Domänennamens einbinden?

Ja, wenn Sie die erforderlichen DNS-Einträge erstellen. Azure NetApp Files liefert die Dienst-IP-Adresse für das bereitgestellte Volume. 

> [!NOTE] 
> Azure NetApp Files kann bei Bedarf weitere IP-Adressen für den Dienst bereitstellen.  DNS-Einträge müssen möglicherweise in regelmäßigen Abständen aktualisiert werden.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kann ich meine eigene IP-Adresse für ein Azure NetApp Files-Volume festlegen oder auswählen?  

Nein. Die IP-Zuweisung zu Azure NetApp Files-Volumes ist dynamisch. Die statische IP-Zuweisung wird nicht unterstützt. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Unterstützt Azure NetApp Files Doppelstapel-VNet (IPv4 und IPv6)?

Nein, Azure NetApp Files unterstützt derzeit kein Doppelstapel-VNet (IPv4 und IPv6).  

### <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>Ist die Anzahl der IP-Adressen, die Azure VMWare Solutions für Gastbetriebssystem-Einbindungen verwenden, [auf 1.000 beschränkt](azure-netapp-files-resource-limits.md#resource-limits)?

Nein. Azure VMWare Solutions befindet sich hinter einem ER-Gateway. Dadurch verhält es sich ähnlich wie ein lokales System. Die Anzahl der AVS-„Hosts“ und -„Gäste“ ist für Azure NetApp Files nicht sichtbar und der Grenzwert von 1.000 IP-Adressen ist nicht anwendbar.
 
## <a name="security-faqs"></a>Häufig gestellte Fragen zur Sicherheit

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kann der Netzwerkdatenverkehr zwischen Azure-VM und Speicher verschlüsselt werden?

Azure NetApp Files-Datenverkehr ist entwurfsbedingt grundsätzlich sicher, da er keinen öffentlichen Endpunkt bereitstellt und der Datenverkehr innerhalb des kundeneigenen VNet verbleibt. Daten während der Übertragung werden nicht standardmäßig verschlüsselt. Der Datenverkehr von einer Azure-VM (auf der ein NFS- oder SMB-Client ausgeführt wird) zu Azure NetApp Files ist jedoch so sicher wie jeder andere Datenverkehr zwischen Azure-VMs. 

Das NFSv3-Protokoll bietet keine Unterstützung für die Verschlüsselung, sodass diese Daten während der Übertragung nicht verschlüsselt werden können. NFSv4.1- und SMB3-Verschlüsselung von Daten während der Übertragung kann jedoch optional aktiviert werden. Der Datenverkehr zwischen NFSv4.1-Clients und Azure NetApp Files-Volumes kann mit Kerberos per AES-256-Verschlüsselung verschlüsselt werden. Weitere Informationen finden Sie unter [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files](configure-kerberos-encryption.md). Der Datenverkehr zwischen SMB3-Clients und Azure NetApp Files-Volumes kann mithilfe des AES-CCM-Algorithmus für SMB 3.0- und mit dem AES-GCM-Algorithmus für SMB 3.1.1-Verbindungen verschlüsselt werden. Ausführliche Informationen finden Sie unter [Erstellen eines SMB-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kann der Speicher im Ruhezustand verschlüsselt werden?

Alle Azure NetApp Files-Volumes werden mit dem FIPS 140-2-Standard verschlüsselt. Alle Schlüssel werden vom Azure NetApp Files-Dienst verwaltet. 

### <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Wird der Datenverkehr bei der regionsübergreifenden Replikation in Azure NetApp Files verschlüsselt?

Alle zwischen dem Quellvolume und dem Zielvolume übertragenen Daten werden bei der regionsübergreifenden Replikation in Azure NetApp Files per TLS 1.2 AES-256 GCM-Verschlüsselung verschlüsselt. Diese Verschlüsselung erfolgt zusätzlich zur [Azure MACSec-Verschlüsselung](../security/fundamentals/encryption-overview.md), die standardmäßig für den gesamten Azure-Datenverkehr aktiviert ist, auch für den Datenverkehr bei der regionsübergreifenden Replikation in Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Wie werden Verschlüsselungsschlüssel verwaltet? 

Die Schlüsselverwaltung für Azure NetApp Files wird vom Dienst verarbeitet. Für jedes Volume wird ein eindeutiger XTS-AES-256-Datenverschlüsselungsschlüssel generiert. Zum Verschlüsseln und Schützen aller Volumeschlüssel wird eine Verschlüsselungsschlüsselhierarchie verwendet. Diese Verschlüsselungsschlüssel werden niemals in unverschlüsseltem Format angezeigt oder gemeldet. Verschlüsselungsschlüssel werden sofort gelöscht, wenn ein Volume gelöscht wird.

Unterstützung von kundenseitig verwalteten Schlüsseln (Bring Your Own Key) mithilfe von Azure Dedicated HSM ist in den Regionen „USA, Osten“, „USA, Süden-Mitte“, „USA, Westen2“ und „US Gov Virginia“ unter Kontrolle verfügbar. Sie können den Zugriff unter [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) anfordern. Entsprechend der zunehmenden Verfügbarkeit von Kapazitäten werden Anforderungen genehmigt.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kann ich Regeln für NFS-Exportrichtlinien konfigurieren, um den Zugriff auf das Azure NetApp Files-Diensteinbindungsziel zu steuern?

Ja, Sie können bis zu fünf Regeln in einer einzigen NFS-Exportrichtlinie konfigurieren.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Unterstützt Azure NetApp Files Netzwerksicherheitsgruppen?

Nein, Sie können derzeit keine Netzwerksicherheitsgruppen auf das delegierte Azure NetApp Files-Subnetz oder die vom Dienst erstellten Netzwerkschnittstellen anwenden.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Kann ich Azure RBAC mit Azure NetApp Files verwenden?

Ja, Azure NetApp Files unterstützt Azure RBAC-Features. Neben den integrierten Azure-Rollen können [benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md) für Azure NetApp Files erstellt werden. 

Eine vollständige Liste der Azure NetApp Files-Berechtigungen finden Sie in den Azure-Ressourcenanbietervorgängen für [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp).

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Werden Azure-Aktivitätsprotokolle für Azure NetApp Files unterstützt?

Bei Azure NetApp Files handelt es sich um einen nativen Azure-Dienst. All PUT-, POST- und DELETE-APIs für Azure NetApp Files werden protokolliert. Die Protokolle geben beispielsweise Aufschluss darüber, wer die Momentaufnahme erstellt oder das Volume geändert hat.

Eine vollständige Liste der API-Vorgänge finden Sie unter [Azure NetApp Files-REST-API](/rest/api/netapp/).

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Kann ich Azure-Richtlinien mit Azure NetApp Files verwenden?

Ja. Sie können [benutzerdefinierte Azure-Richtlinien](../governance/policy/tutorials/create-custom-policy-definition.md) erstellen. 

Sie können allerdings keine Azure-Richtlinien (benutzerdefinierte Benennungsrichtlinien) für die Azure NetApp Files-Schnittstelle erstellen. Lesen Sie den Artikel mit den [Richtlinien für die Azure NetApp Files-Netzwerkplanung](azure-netapp-files-network-topologies.md#considerations).

### <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Werden die Daten sicher gelöscht, wenn ich ein Azure NetApp Files-Volume lösche? 

Das Löschen eines Azure NetApp Files-Volumes erfolgt programmgesteuert und mit sofortiger Wirkung. Der Löschvorgang umfasst das Löschen von Schlüsseln, die zum Verschlüsseln der ruhenden Daten verwendet werden. Es gibt keine Möglichkeit, ein gelöschtes Volume wiederherzustellen, nachdem der Löschvorgang erfolgreich ausgeführt wurde (über Schnittstellen wie das Azure-Portal und die API).

## <a name="performance-faqs"></a>Häufig gestellte Fragen zur Leistung

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Was kann ich tun, um die Leistung von Azure NetApp Files zu optimieren?

Je nach Leistungsanforderungen können Sie folgende Aktionen ausführen: 
- Stellen Sie sicher, dass die VM eine geeignete Größe aufweist.
- Aktivieren Sie den beschleunigten Netzwerkbetrieb für die VM.
- Wählen Sie die gewünschte Dienstebene und -größe für den Kapazitätspool.
- Erstellen Sie ein Volume mit der gewünschten Kontingentgröße für Kapazität und Leistung.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Wie konvertiere ich durchsatzbasierte Dienstebenen von Azure NetApp Files zu IOPS?

Sie können MB/s mit der folgenden Formel zu IOPS konvertieren:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Wie ändere ich die Dienstebene eines Volumes?

Sie können den Servicelevel eines vorhandenen Volumes ändern, indem Sie das Volume in einen anderen Kapazitätspool verschieben, der den von Ihnen für das Volume gewünschten [Servicelevel](azure-netapp-files-service-levels.md) verwendet. Siehe [Dynamisches Ändern der Dienstebene eines Volumes](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Wie überwache ich die Leistung von Azure NetApp Files?

Azure NetApp Files bietet Metriken zum Messen der Volumeleistung. Sie können auch Azure Monitor verwenden, um die Nutzungsmetriken für Azure NetApp Files zu überwachen.  Eine Liste der Leistungsmetriken für Azure NetApp Files finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md).

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Was sind die Leistungsauswirkungen von Kerberos auf NFSv4.1?

Informationen zu den Sicherheitsoptionen für NFSv4.1, den getesteten Leistungsvektoren und den erwarteten Leistungsauswirkungen finden Sie unter [Leistungsauswirkungen von Kerberos auf NFSv4.1-Volumes](performance-impact-kerberos.md). 

### <a name="does-azure-netapp-files-support-smb-direct"></a>Unterstützt Azure NetApp Files SMB Direct?

Nein, Azure NetApp Files unterstützt SMB Direct nicht. 

### <a name="is-nic-teaming-supported-in-azure"></a>Wird NIC-Teaming von Azure unterstützt?

NIC-Teaming wird von Azure nicht unterstützt. Auch wenn auf virtuellen Azure-Computern mehrere Netzwerkschnittstellen unterstützt werden, stellen sie eher ein logisches als ein physisches Konstrukt dar. Daher bieten sie keine Fehlertoleranz.  Außerdem wird die Bandbreite, die für einen virtuellen Azure-Computer verfügbar ist, für den Computer selbst und nicht für einzelne Netzwerkschnittstellen berechnet.

### <a name="are-jumbo-frames-supported"></a>Werden Großrahmen unterstützt?

Großrahmen werden für virtuelle Azure-Computer nicht unterstützt.

## <a name="nfs-faqs"></a>Häufig gestellte Fragen zu NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ich möchte, dass automatisch ein Volume eingebunden wird, wenn eine Azure-VM gestartet oder neu gestartet wird.  Wie konfiguriere ich meinen Host für persistente NFS-Volumes?

Damit beim Starten oder Neustarten einer VM automatisch ein NFS-Volume eingebunden wird, fügen Sie einen Eintrag für die Datei `/etc/fstab` zum Host hinzu. 

Weitere Informationen finden Sie unter [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Welche NFS-Version wird von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt NFSv3 und NFSv4.1. Für die [Volumeerstellung](azure-netapp-files-create-volumes.md) können beide NFS-Versionen verwendet werden. 

### <a name="how-do-i-enable-root-squashing"></a>Wie aktiviere ich das Root Squashing?

Sie können angeben, ob für das Stammkonto (Root) Zugriff auf das Volume besteht, indem Sie die Exportrichtlinie des Volumes verwenden. Weitere Informationen finden Sie unter [Konfigurieren der Exportrichtlinie für ein NFS-Volume](azure-netapp-files-configure-export-policy.md).

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Kann ich den gleichen Dateipfad (Volumeerstellungstoken) für mehrere Volumes verwenden?

Ja, das ist möglich. Der Dateipfad muss jedoch entweder in einem anderen Abonnement oder in einer anderen Region verwendet werden.   

Ein Beispiel: Angenommen, Sie erstellen ein Volume namens `vol1`. Anschließend erstellen Sie ein weiteres Volume mit dem Namen `vol1` in einem anderen Kapazitätspool, aber im gleichen Abonnement und in der gleichen Region. In diesem Fall führt die Verwendung des gleichen Volumenamens (`vol1`) zu einem Fehler. Wenn Sie den gleichen Dateipfad verwenden möchten, muss sich der Name in einer anderen Region oder in einem anderen Abonnement befinden.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Warum nimmt der Client für das Durchsuchen von Ordnern und Unterordnern viel Zeit in Anspruch, wenn ich versuche, über einen Windows-Client auf NFS-Volumes zuzugreifen?

Stellen Sie sicher, dass `CaseSensitiveLookup` auf dem Windows-Client aktiviert ist, um die Suche nach Ordnern und Unterordnern zu beschleunigen:

1. Verwenden Sie den folgenden PowerShell-Befehl, um „CaseSensitiveLookup“ aktivieren:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Binden Sie das Volume auf dem Windows-Server ein.   
    Beispiel:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Wie unterstützt Azure NetApp Files die Dateisperrung von NFSv4.1? 

Für NFSv4.1-Clients unterstützt Azure NetApp Files den NFSv4.1-Mechanismus für die Dateisperrung, der den Status aller Dateisperren unter einem leasebasierten Modell aufrechterhält. 

Gemäß RFC 3530 definiert Azure NetApp Files eine einzelne Leasedauer für alle Zustände, die ein NFS-Client annimmt. Wenn der Client seine Leasedauer nicht innerhalb des definierten Zeitraums erneuert, werden alle Zustände, die mit der Leasedauer des Clients verbunden sind, vom Server freigegeben.  

Wenn z. B. ein Client, der ein Volume einbinden will, nicht mehr reagiert oder nach Ablauf des Timeouts abstürzt, werden die Sperren freigegeben. Der Client kann seine Leasedauer explizit oder implizit erneuern, indem er Vorgänge wie das Lesen einer Datei durchführt.   

Eine Toleranzperiode definiert einen Zeitraum für eine spezielle Verarbeitung, in dem Clients versuchen können, ihren Sperrstatus während einer Serverwiederherstellung freizugeben. Der Standardtimeout für die Leasedauer beträgt 30 Sekunden mit einer Toleranzperiode von 45 Sekunden. Nach dieser Zeit wird die Leasedauer des Clients freigegeben.   

## <a name="smb-faqs"></a>Häufig gestellte Fragen zu SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Welche SMB-Versionen werden von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt SMB 2.1 und SMB 3.1 (Unterstützung für SMB 3.0 ist hierbei eingeschlossen).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Ist für den SMB-Zugriff eine Active Directory-Verbindung erforderlich? 

Ja, Sie müssen eine Active Directory-Verbindung erstellen, bevor Sie ein SMB-Volume bereitstellen. Damit erfolgreich eine Verbindung hergestellt werden kann, muss das delegierte Subnetz von Azure NetApp Files auf die angegebenen Domänencontroller zugreifen können.  Ausführliche Informationen dazu finden Sie unter [Erstellen eines SMB-Volumes](./azure-netapp-files-create-volumes-smb.md). 

### <a name="how-many-active-directory-connections-are-supported"></a>Wie viele Active Directory-Verbindungen werden unterstützt?

Sie können nur eine Active Directory-Verbindung (AD) pro Abonnement und pro Region konfigurieren. Weitere Informationen finden Sie unter [Anforderungen für Active Directory-Verbindungen](create-active-directory-connections.md#requirements-for-active-directory-connections). 

Sie können jedoch mehrere NetApp-Konten, die sich unter demselben Abonnement und derselben Region befinden, einem allgemeinen AD-Server zuordnen, der in einem der NetApp-Konten erstellt wurde. Weitere Informationen finden Sie unter [Zuordnen mehrerer NetApp-Konten im selben Abonnement und derselben Region zu einer AD-Verbindung](create-active-directory-connections.md#shared_ad). 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Unterstützt Azure NetApp Files Azure Active Directory? 

Sowohl [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) als auch [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) wird unterstützt. Bereits vorhandene Active Directory-Domänencontroller können mit Azure NetApp Files verwendet werden. Domänencontroller können als virtuelle Computer in Azure oder lokal über ExpressRoute oder S2S-VPN betrieben werden. Azure NetApp Files unterstützt aktuell keine AD-Einbindung für [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/).

Bei Verwendung von Azure NetApp Files mit Azure Active Directory Domain Services lautet der Pfad der Organisationseinheit `OU=AADDC Computers`, wenn Sie Active Directory für Ihr NetApp-Konto konfigurieren.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Welche Versionen von Windows Server Active Directory werden unterstützt?

Azure NetApp Files unterstützt die Windows Server-Versionen 2008 R2 SP1 – 2019 von Active Directory Domain Services.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Ich habe Probleme beim Herstellen einer Verbindung mit meiner SMB-Freigabe.   Wie sollte ich vorgehen?

Legen Sie als bewährte Vorgehensweise die maximale Toleranz für die Synchronisierung der Computeruhr auf fünf Minuten fest. Weite Informationen finden Sie unter [Maximale Toleranz für die Synchronisierung der Computeruhr](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Kann ich `SMB Shares`, `Sessions` und `Open Files` über die Computerverwaltung (MMC) verwalten?

Die Verwaltung von `SMB Shares`, `Sessions` und `Open Files` über die Computerverwaltung (MMC) wird derzeit nicht unterstützt.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Wie kann ich die IP-Adresse eines SMB-Volumes über das Portal abrufen?

Verwenden Sie den Link **JSON-Ansicht** im Bereich mit der Volumeübersicht, und suchen Sie unter **properties** -> **mountTargets** nach dem Bezeichner **startIp**.

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Kann eine Azure NetApp Files-SMB-Freigabe als Stamm für den DFS-Namespace (DFS-N) fungieren?

Nein. Azure NetApp Files-SMB-Freigaben können jedoch als Ordnerziel für den DFS-Namespace (DFS-N) dienen.   
Um eine Azure NetApp Files-SMB-Freigabe als DFS-N-Ordnerziel zu verwenden, geben Sie den UNC-Einbindungspfad (Universal Naming Convention) der Azure NetApp Files-SMB-Freigabe entsprechend den Schritten zum [Hinzufügen von Ordnerzielen](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target) an.  

### <a name="can-the-smb-share-permissions-be-changed"></a>Können die SMB-Freigabeberechtigungen geändert werden?   

Nein, die Freigabeberechtigungen können nicht geändert werden. Die NTFS-Berechtigungen des `root`-Volumes können jedoch mithilfe des [NTFS-Datei- und -Ordnerberechtigungsverfahrens](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions) geändert werden. 

## <a name="capacity-management-faqs"></a>Häufig gestellte Fragen zur Kapazitätsverwaltung

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Wie überwache ich die Nutzung für den Kapazitätspool und das Volume von Azure NetApp Files? 

Azure NetApp Files stellt Nutzungsmetriken für Kapazitätspool und Volume bereit. Sie können auch Azure Monitor verwenden, um die Nutzung für Azure NetApp Files zu überwachen. Ausführliche Informationen finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md). 

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Wie bestimme ich, ob ein Verzeichnis dabei ist, sein Größenlimit zu erreichen?

Sie können den Befehl `stat` von einem Client aus verwenden, um festzustellen, ob sich ein Verzeichnis der [maximal zulässigen Größe](azure-netapp-files-resource-limits.md#resource-limits) für Verzeichnismetadaten (320 MB) nähert.
Informationen zum Grenzwert und zur Berechnung finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md#directory-limit). 

<!-- 
You can use the `stat` command from a client to see whether a directory is approaching the maximum size limit for directory metadata (320 MB).   

For a 320-MB directory, the number of blocks is 655360, with each block size being 512 bytes.  (That is, 320x1024x1024/512.)  This number translates to approximately 4 million files maximum for a 320-MB directory. However, the actual number of maximum files might be lower, depending on factors such as the number of files containing non-ASCII characters in the directory. As such, you should use the `stat` command as follows to determine whether your directory is approaching its limit.  

Examples:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```
--> 

### <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>Zählt der Speicherplatz für Momentaufnahmen zur nutzbaren oder bereitgestellten Kapazität eines Volumes?

Ja, die für Momentaufnahmen [genutzte Kapazität](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) zählt zum bereitgestellten Speicherplatz im Volume. Wenn das Volume vollständig ausgelastet ist, kommen folgende Maßnahmen in Frage:

* [Ändern Sie die Größe des Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md).
* [Entfernen Sie ältere Momentaufnahmen](azure-netapp-files-manage-snapshots.md#delete-snapshots), um Speicherplatz auf dem Hostingvolume freizugeben. 

### <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Wird in Azure NetApp Files die automatische Vergrößerung für Volumes oder Kapazitätspools unterstützt?

Nein, Volumes und Kapazitätspools werden in Azure NetApp Files beim Auffüllen nicht automatisch vergrößert. Weitere Informationen finden Sie unter [Kostenmodell für Azure NetApp Files](azure-netapp-files-cost-model.md).   

Sie können das von der Community unterstützte [Logic Apps-Tool ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager) verwenden, um kapazitätsbasierte Warnungsregeln zu verwalten. Das Tool kann die Volumegröße automatisch erhöhen, um zu verhindern, dass auf Ihren Volumes kein Speicherplatz mehr verfügbar ist.

### <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>Wird das Zielvolume einer Replikation auf das feste Volumekontingent angerechnet?  

Nein, das Zielvolume einer Replikation wird nicht auf das feste Volumekontingent angerechnet.

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kann ich Azure NetApp Files über den Azure Storage-Explorer verwalten?

Nein. Azure NetApp Files wird vom Azure Storage-Explorer nicht unterstützt.

## <a name="data-migration-and-protection-faqs"></a>Häufig gestellte Fragen zu Datenmigration und -schutz

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Wie migriere ich Daten zu Azure NetApp Files?
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Migrieren von Daten zum Dienst verwenden. 

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Datenmigration aus einer lokalen Umgebung nach Azure NetApp Files gelten folgende Anforderungen: 

- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen der Quelle und der IP-Adresse des Azure NetApp Files-Zielvolumes. Die Datenübertragung zwischen der lokalen Umgebung und dem Azure NetApp Files-Dienst wird über ExpressRoute unterstützt.
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

### <a name="where-does-azure-netapp-files-store-customer-data"></a>Wo speichert Azure NetApp Files Kundendaten?   

Standardmäßig verbleiben Ihre Daten in der Region, in der Sie Ihre Azure NetApp Files-Volumes bereitstellen. Sie können sich jedoch dafür entscheiden, Ihre Daten auf Volume-für-Volume-Basis in verfügbare Zielregionen zu replizieren, indem Sie [regionsübergreifende Replikation](cross-region-replication-introduction.md) verwenden.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Wie erstelle ich eine Kopie eines Azure NetApp Files-Volumes in einer anderen Azure-Region?
    
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Replizieren von Daten zwischen Azure-Regionen verwenden. 

Mit der Funktion zur [regionsübergreifenden Replikation](cross-region-replication-introduction.md) können Sie Daten asynchron aus einem Azure NetApp Files-Volume (Quelle) in einer Region in ein anderes Azure NetApp Files-Volume (Ziel) in einer anderen Region replizieren.  Außerdem können Sie [unter Verwendung einer Momentaufnahme eines vorhandenen Volumes ein neues Volume erstellen](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume).

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Replikation eines Azure NetApp Files-Volumes in eine andere Azure-Region gelten folgende Anforderungen: 
- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen VNETs in jeder Region. Derzeit wird ein globales Peering zwischen VNETs nicht unterstützt.  Sie können Konnektivität zwischen VNETs herstellen, indem Sie eine Verbindung über eine ExpressRoute-Leitung erstellen oder eine S2S-VPN-Verbindung verwenden. 
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

### <a name="is-migration-with-azure-data-box-supported"></a>Wird die Migration mit Azure Data Box unterstützt?

Nein. Azure Data Box unterstützt Azure NetApp Files derzeit nicht. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Wird die Migration mit dem Azure Import/Export-Dienst unterstützt?

Nein. Der Azure Import/Export-Dienst unterstützt Azure NetApp Files derzeit nicht.

## <a name="product-faqs"></a>Häufig gestellte Fragen (FAQs) zu Produkten

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Kann ich NFS- oder SMB-Volumes von Azure NetApp Files mit Azure VMware Solution (AVS) verwenden?

Sie können NFS-Volumes von Azure NetApp Files auf Windows-VMs oder Linux-VMs von AVS einbinden. Sie können SMB-Freigaben von Azure NetApp Files auf Windows-VMs von AVS zuordnen. Weitere Informationen finden Sie unter [Azure NetApp Files mit Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Welche Regionen werden für die Verwendung von NFS- oder SMB-Volumes von Azure NetApp Files mit Azure VMware Solution (AVS) unterstützt?

Die Nutzung von Azure NetApp Files-NFS- oder -SMB-Volumes mit AVS für *Gastbetriebssystem-Einbindungen* wird in [allen AVS- and ANF-fähigen Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp) unterstützt.

### <a name="does-azure-netapp-files-work-with-azure-policy"></a>Kann Azure NetApp Files mit Azure Policy verwendet werden?

Ja. Azure NetApp Files ist ein Erstanbieterdienst. Er entspricht vollständig den Standards für Azure-Ressourcenanbieter. Somit kann Azure NetApp Files über *benutzerdefinierte Richtliniendefinitionen* in Azure Policy integriert werden. Informationen zum Implementieren von benutzerdefinierten Richtlinien für Azure NetApp Files finden Sie in der Microsoft Tech Community unter [Azure Policy now available for Azure NetApp Files](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258) (Azure Policy jetzt für Azure NetApp Files verfügbar). 

### <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>Welche Unicode-Zeichencodierung wird von Azure NetApp Files für das Erstellen und Anzeigen von Datei- und Verzeichnisnamen unterstützt?   

Azure NetApp Files unterstützt nur Datei- und Verzeichnisnamen, die mit der UTF-8-Zeichencodierung für NFS- und SMB-Volumes codiert sind.

Wenn Sie versuchen, Dateien oder Verzeichnisse mit Namen zu erstellen, die ergänzende Zeichen oder Ersatzzeichenpaare verwenden (z. B. nicht reguläre Zeichen oder Emojis, die von UTF-8 nicht unterstützt werden), dann schlägt der Vorgang fehl. Eine Fehlermeldung des Windows-Client lautet in diesem Fall z. B.: „Der angegebene Dateiname ist ungültig oder zu lang. Geben Sie einen anderen Dateinamen an.” 

## <a name="next-steps"></a>Nächste Schritte  

- [Häufig gestellte Fragen zu Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Häufig gestellte Fragen zu Microsoft Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Häufig gestellte Fragen zur Leistung von SMB für Azure NetApp Files](azure-netapp-files-smb-performance.md)
