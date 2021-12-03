---
title: Planung für eine Azure Files-Bereitstellung | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zur Planung einer Azure Files-Bereitstellung. Sie können entweder eine Azure-Dateifreigabe direkt einbinden oder Azure-Dateifreigaben mit Azure-Dateisynchronisierung lokal zwischenspeichern.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 407d3c8d14cec2a55a9a33d58dfa1af77b8266b7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522838"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planung für eine Azure Files-Bereitstellung
[Azure Files](storage-files-introduction.md) kann auf zwei Arten bereitgestellt werden: durch direktes Einbinden der serverlosen Azure-Dateifreigaben oder durch lokales Zwischenspeichern von Azure-Dateifreigaben mithilfe von Azure-Dateisynchronisierung. Welche Bereitstellungsoption Sie auswählen, ändert die Aspekte, die Sie beim Planen der Bereitstellung berücksichtigen müssen. 

- **Direktes Einbinden einer Azure-Dateifreigabe**: Da Azure Files entweder Zugriff auf den Server Message Block (SMB) oder das Network File System (NFS) bereitstellt, können Sie Azure-Dateifreigaben lokal oder in der Cloud mithilfe der SMB- oder NFS-Standardclients einbinden, die in Ihrem Betriebssystem verfügbar sind. Da Azure-Dateifreigaben serverlos sind, erfordert die Bereitstellung für Produktionsszenarien keine Verwaltung eines Dateiservers oder NAS-Geräts. Dies bedeutet, dass Sie keine Softwarepatches anwenden oder physische Datenträger austauschen müssen. 

- **Lokales Zwischenspeichern von Azure-Dateifreigaben mit Azure-Dateisynchronisierung**: Die Azure-Dateisynchronisierung ermöglicht das Zentralisieren der Dateifreigaben Ihrer Organisation in Azure Files, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Die Azure-Dateisynchronisierung transformiert Ihre lokalen Windows Server-Computer in einen schnellen Cache für Ihre Azure-SMB-Dateifreigabe. 

Dieser Artikel befasst sich hauptsächlich mit Bereitstellungsüberlegungen zum Bereitstellen einer Azure-Dateifreigabe, die direkt von einem lokalen oder cloudbasierten Client eingebunden werden soll. Weitere Informationen zum Planen einer Azure-Dateisynchronisierungsbereitstellung finden Sie unter [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md).

## <a name="available-protocols"></a>Verfügbare Protokolle
Azure Files unterstützt zwei Branchenstandardprotokolle für die Einbindung von Azure-Dateifreigaben: das [SMB-Protokoll (Server Message Block)](files-smb-protocol.md) und das [NFS-Protokoll (Network File System)](files-nfs-protocol.md). In Azure Files können Sie das Dateisystemprotokoll auswählen, das sich für Ihre Workload am besten eignet. Azure-Dateifreigaben unterstützen nicht SMB- und NFS-Protokolle auf derselben Dateifreigabe. Sie können jedoch SMB- und NFS-Azure-Dateifreigaben innerhalb desselben Speicherkontos erstellen. NFS 4.1 wird derzeit nur innerhalb des neuen **FileStorage**-Speicherkontotyps unterstützt (nur Premium-Dateifreigaben).

Mit sowohl SMB- als auch NFS-Dateifreigaben bietet Azure Files Dateifreigaben in Unternehmensqualität, die entsprechend Ihren Speicheranforderungen hochskaliert werden können und auf die Tausende von Clients gleichzeitig zugreifen können.

| Funktion | SMB | NFS |
|---------|-----|---------------|
| Unterstützte Protokollversionen | SMB 3.1.1, SMB 3.0, SMB 2.1 | NFS 4.1 |
| Empfohlenes Betriebssystem | <ul><li>Windows 10, Version 21H1 und höher</li><li>Windows Server 2019 und höher</li><li>Linux-Kernelversion 5.3 und höher</li></ul> | Linux-Kernelversion 4.3 und höher |
| [Verfügbare Tarife](storage-files-planning.md#storage-tiers)  | Premium, transaktionsoptimiert, heiß, kalt | Premium |
| Abrechnungsmodell | <ul><li>[Bereitgestellte Kapazität für Premium-Dateifreigaben](./understanding-billing.md#provisioned-model)</li><li>[Pay-as-you-go für Standarddateifreigaben](./understanding-billing.md#pay-as-you-go-model)</li></ul> | [Bereitgestellte Kapazität](./understanding-billing.md#provisioned-model) |
| [Redundanz](storage-files-planning.md#redundancy) | LRS, ZRS, GRS, GZRS | LRS, ZRS |
| Dateisystemsemantik | Win32 | POSIX |
| Authentifizierung | Identitätsbasierte Authentifizierung (Kerberos), Authentifizierung mit gemeinsam verwendeten Schlüsseln (NTLMv2) | Hostbasierte Authentifizierung |
| Autorisierung | Win32-Zugriffssteuerungslisten (Access Control Lists, ACLs) | Berechtigungen im UNIX-Format |
| Groß- und Kleinschreibung | Keine Beachtung von Groß-/Kleinschreibung, Schreibweise wird beibehalten | Groß-/Kleinschreibung beachten |
| Löschen oder Ändern geöffneter Dateien | Nur mit Sperre | Ja |
| Dateifreigabe | [Windows-Freigabemodus](/windows/win32/fileio/creating-and-opening-files) | Netzwerksperrungs-Manager im Bytebereich (Empfehlung) |
| Unterstützung fester Links | Nicht unterstützt | Unterstützt |
| Unterstützung von symbolischen Verknüpfungen | Nicht unterstützt | Unterstützt |
| Optional über das Internet zugänglich | Ja (nur SMB 3.0 und höher) | Nein |
| Unterstützt FileREST | Ja | Teilmenge: <br /><ul><li>[`FileService`-Vorgänge](/rest/api/storageservices/operations-on-the-account--file-service-)</li><li>[`FileShares`-Vorgänge](/rest/api/storageservices/operations-on-shares--file-service-)</li><li>[`Directories`-Vorgänge](/rest/api/storageservices/operations-on-directories)</li><li>[`Files`-Vorgänge](/rest/api/storageservices/operations-on-files)</li></ul> |

## <a name="management-concepts"></a>Verwaltungskonzepte
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Beim Bereitstellen von Azure-Dateifreigaben in Speicherkonten wird Folgendes empfohlen:

- Ausschließliches Bereitstellen von Azure-Dateifreigaben in Speicherkonten mit anderen Azure-Dateifreigaben. Auch wenn GPv2-Speicherkonten Speicherkonten mit verschiedenen Zwecken ermöglichen, kann das Mischen von Ressourcen das spätere Beheben von Leistungsproblemen erschweren, da für Speicherressourcen wie z. B. Azure-Dateifreigaben und Blobcontainer die Grenzen des Speicherkontos gemeinsam sind. 

- Beachten Sie beim Bereitstellen von Azure-Dateifreigaben die IOPS-Einschränkungen eines Speicherkontos. Im Idealfall würden Sie Dateifreigaben Speicherkonten im Verhältnis 1:1 zuordnen. Dies ist jedoch ggf. aufgrund verschiedener Beschränkungen und Einschränkungen in Ihrer Organisation oder in Azure nicht immer möglich. Wenn es nicht möglich ist, dass nur eine Dateifreigabe in einem Speicherkonto bereitgestellt wird, sollten Sie berücksichtigen, welche Freigaben sehr aktiv sein werden und welche Freigaben weniger aktiv sind, um sicherzustellen, dass die aktivsten Dateifreigaben nicht zusammen im gleichen Speicherkonto gruppiert werden.

- Stellen Sie nur GPv2- und FileStorage-Konten bereit, und aktualisieren Sie GPv1- und klassische Speicherkonten, wenn diese in Ihrer Umgebung vorhanden sind. 

## <a name="identity"></a>Identity
Für den Zugriff auf eine Azure-Dateifreigabe muss der Benutzer der Dateifreigabe authentifiziert sein und über Autorisierung für den Zugriff auf die Freigabe verfügen. Dies erfolgt basierend auf der Identität des Benutzers, der auf die Dateifreigabe zugreift. Azure Files ist in drei Hauptidentitätsanbieter integriert:
- **Lokale Active Directory Domain Services (AD DS oder lokale AD DS)** : Azure Storage-Konten können über Domänenbeitritt wie ein Windows Server-Dateiserver oder ein NAS-Gerät mit einem kundeneigenen Active Directory Domain Services verbunden werden. Sie können einen Domänencontroller lokal, auf einer Azure-VM oder sogar als VM bei einem anderen Cloudanbieter bereitstellen. Azure Files ist unabhängig vom Speicherort des Domänencontrollers. Sobald ein Speicherkonto einer Domäne beigetreten ist, kann der Endbenutzer eine Dateifreigabe mit dem Benutzerkonto einbinden, mit dem er sich bei seinem PC angemeldet hat. Bei der AD-basierten Authentifizierung wird das Kerberos-Authentifizierungsprotokoll verwendet.
- **Azure Active Directory Domain Services (Azure AD DS)** : Azure AD DS bietet einen von Microsoft verwalteten Domänencontroller, der für Azure-Ressourcen verwendet werden kann. Der Domänenbeitritt Ihres Speicherkontos zu Azure AD DS bietet ähnliche Vorteile wie der Domänenbeitritt zu einem Active Directory im Besitz eines Kunden. Diese Bereitstellungsoption ist besonders nützlich für Lift-and-Shift-Anwendungsszenarien, die AD-basierte Berechtigungen erfordern. Da Azure AD DS AD-basierte Authentifizierung bereitstellt, verwendet diese Option auch das Kerberos-Authentifizierungsprotokoll.
- **Azure-Speicherkontoschlüssel**: Azure-Dateifreigaben können auch mit einem Azure-Speicherkontoschlüssel bereitgestellt werden. Bei einer solchen Einbindung einer Dateifreigabe wird der Speicherkontoname als Benutzername und der Speicherkontoschlüssel als Kennwort verwendet. Die Verwendung des Speicherkontoschlüssels zum Einbinden der Azure-Dateifreigabe ist praktisch ein Administratorvorgang, da die eingebundene Dateifreigabe über vollständige Berechtigungen für alle Dateien und Ordner auf der Freigabe verfügt, auch wenn diese über ACLs verfügen. Wenn Sie den Speicherkontoschlüssel zum Einbinden über SMB verwenden, wird das NTLMv2-Authentifizierungsprotokoll verwendet.

Für Kunden, die von lokalen Dateiservern migrieren oder neue Dateifreigaben in Azure Files erstellen, die sich wie Windows-Dateiserver oder NAS-Geräte verhalten sollen, ist der Domänenbeitritt des Speicherkontos zum **Active Directory im Besitz des Kunden** die empfohlene Option. Weitere Informationen zum Domänenbeitritt Ihres Speicherkontos zu einem Active Directory im Besitz des Kunden finden Sie unter [Azure Files Active Directory: Übersicht](storage-files-active-directory-overview.md).

Wenn Sie beabsichtigen, den Speicherkontoschlüssel für den Zugriff auf Ihre Azure-Dateifreigaben zu verwenden, empfehlen wir die Verwendung von Dienstendpunkten, wie im Abschnitt [Netzwerk](#networking) beschrieben.

## <a name="networking"></a>Netzwerk
Auf Azure-Dateifreigaben kann über den öffentlichen Endpunkt des Speicherkontos von überall aus zugegriffen werden. Dies bedeutet, dass authentifizierte Anforderungen (etwa Anforderungen, die durch die Anmeldeidentität eines Benutzer autorisiert wurden) auf sichere Weise verwendet werden können – ganz gleich, ob ihr Ursprung innerhalb oder außerhalb von Azure liegt. In vielen Kundenumgebungen ist die Einbindung der Azure-Dateifreigabe in die lokale Arbeitsstation zunächst nicht erfolgreich, obwohl Einbindungen von virtuellen Azure-Computern problemlos funktionieren. Dies liegt daran, dass viele Organisationen und Internetdienstanbieter (Internet Service Providers, ISPs) Port 445 blockieren, der von SMB für die Kommunikation verwendet wird. Um eine Zusammenfassung der ISPs anzuzeigen, die den Zugang von Port 445 aus zulassen oder verweigern, gehen Sie auf [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Zum Aufheben der Blockierung des Zugriffs auf Ihre Azure-Dateifreigabe stehen Ihnen zwei Hauptoptionen zur Verfügung:

- Entsperren von Port 445 für das lokale Netzwerk Ihrer Organisation. Auf Azure-Dateifreigaben kann nur extern über den öffentlichen Endpunkt zugegriffen werden, indem internetsichere Protokolle wie SMB 3.x und die FileREST-API verwendet werden. Dies ist die einfachste Möglichkeit, vom lokalen Standort aus auf Ihre Azure-Dateifreigabe zuzugreifen, da keine erweiterte Netzwerkkonfiguration erforderlich ist, die über das Ändern von Regeln für den Port für ausgehenden Datenverkehr Ihres Unternehmens hinausgeht. Es wird jedoch empfohlen, Legacy- und veraltete Versionen des SMB-Protokolls zu entfernen (also SMB 1.0). Weitere Informationen hierzu finden Sie unter [Sichern von Windows/Windows Server](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3) und [Sichern von Linux](files-remove-smb1-linux.md).

- Zugreifen auf Azure-Dateifreigaben über eine ExpressRoute- oder VPN-Verbindung. Wenn Sie über einen Netzwerktunnel auf Ihre Azure-Dateifreigabe zugreifen, können Sie Ihre Azure-Dateifreigabe wie eine lokale Dateifreigabe einbinden, da der SMB-Datenverkehr Ihre Organisationsgrenze nicht überschreitet.   

Obwohl es aus technischer Sicht wesentlich einfacher ist, ihre Azure-Dateifreigaben über den öffentlichen Endpunkt einzubinden, erwarten wir, dass die meisten Kunden ihre Azure-Dateifreigaben über eine ExpressRoute- oder VPN-Verbindung einbinden. Die Einbindung mit diesen Optionen ist mit SMB- und NFS-Freigaben möglich. Zu diesem Zweck müssen Sie Folgendes für Ihre Umgebung konfigurieren:  

- **Netzwerktunnelung mithilfe von ExpressRoute, Site-to-Site- oder Point-to-Site-VPN**: Das Tunneln in ein virtuelles Netzwerk ermöglicht den Zugriff auf Azure-Dateifreigaben vom lokalen Standort aus, auch wenn Port 445 blockiert ist.
- **Private Endpunkte**: Private Endpunkte stellen Ihrem Speicherkonto eine dedizierte IP-Adresse innerhalb des Adressraums des virtuellen Netzwerks zur Verfügung. Dies ermöglicht Netzwerktunnelung, ohne dass lokale Netzwerke für alle IP-Adressbereiche, die sich im Besitz der Azure-Speichercluster befinden, geöffnet werden müssen. 
- **DNS-Weiterleitung**: Konfigurieren Sie das lokale DNS, um den Namen Ihres Speicherkontos (`storageaccount.file.core.windows.net` für die öffentlichen Cloudregionen) aufzulösen, um die IP-Adresse Ihrer privaten Endpunkte aufzulösen.

> [!Important]  
> Azure Files unterstützt mehrere Netzwerkroutingoptionen. Die Standardoption, das Microsoft-Routing, kann bei allen Azure Files-Konfigurationen verwendet werden. Die Internetroutingoption unterstützt keine Szenarien mit AD-Domänenbeitritt oder Azure-Dateisynchronisierung.

Informationen zum Planen der Netzwerke, die mit der Bereitstellung einer Azure-Dateifreigabe verbunden sind, finden Sie unter [Überlegungen zum Azure Files-Netzwerk](storage-files-networking-overview.md).

## <a name="encryption"></a>Verschlüsselung
Azure Files unterstützt zwei verschiedene Verschlüsselungstypen: Verschlüsselung während der Übertragung, die sich auf die Verschlüsselung bezieht, die beim Einbinden/Zugreifen auf die Azure-Dateifreigabe verwendet wird, sowie die Verschlüsselung ruhender Daten, die sich darauf bezieht, wie die Daten beim Speichern auf einem Datenträger verschlüsselt werden. 

### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

> [!IMPORTANT]
> In diesem Abschnitt wird die Verschlüsselung während der Übertragung für SMB-Freigaben behandelt. Ausführliche Informationen zur Verschlüsselung während der Übertragung mit NFS-Freigaben finden Sie unter [Sicherheit und Netzwerke](files-nfs-protocol.md#security-and-networking).

Standardmäßig ist in allen Azure-Speicherkonten die Verschlüsselung während der Übertragung aktiviert. Das bedeutet Folgendes: Wenn Sie eine Dateifreigabe über SMB einbinden oder über das FileREST-Protokoll darauf zugreifen (z. B. über das Azure-Portal, PowerShell/CLI oder Azure-SDKs), lässt Azure Files die Verbindung nur dann zu, wenn sie über SMB 3.x mit Verschlüsselung oder über HTTPS hergestellt wird. Clients, die SMB 3.x nicht unterstützen, oder Clients, die zwar SMB 3.x, aber nicht die SMB-Verschlüsselung unterstützen, können die Azure-Dateifreigabe nicht einbinden, wenn die Verschlüsselung während der Übertragung aktiviert ist. Weitere Informationen dazu, welche Betriebssysteme SMB 3.x mit Verschlüsselung unterstützen, finden Sie in der ausführlichen Dokumentation zu [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) und [Linux](storage-how-to-use-files-linux.md). Alle aktuellen PowerShell-, CLI- und SDK-Versionen unterstützen HTTPS.  

Sie können die Verschlüsselung während der Übertragung für ein Azure-Speicherkonto deaktivieren. Wenn die Verschlüsselung deaktiviert ist, lässt Azure Files auch SMB 2.1, SMB 3.x ohne Verschlüsselung und nicht verschlüsselte FileREST-API-Aufrufe über HTTP zu. Der Hauptgrund für die Deaktivierung der Verschlüsselung während der Übertragung ist die Unterstützung einer älteren Anwendung, die unter einem älteren Betriebssystem wie z. B. Windows Server 2008 R2 oder einer älteren Linux-Distribution ausgeführt werden muss. Azure Files lässt nur SMB 2.1-Verbindungen innerhalb der gleichen Region zu, in der sich auch die Azure-Dateifreigabe befindet. Ein SMB 2.1-Client außerhalb der Azure-Region der Azure-Dateifreigabe – z. B. ein lokales System oder eine andere Azure-Region – kann nicht auf die Dateifreigabe zugreifen.

Wir empfehlen dringend, sicherzustellen, dass die Verschlüsselung von Daten während der Übertragung aktiviert ist.

Weitere Informationen zur Verschlüsselung während der Übertragung finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Schutz von Daten
Azure Files umfasst einen mehrschichtigen Ansatz, um sicherzustellen, dass Ihre Daten gesichert, wiederhergestellt und vor Sicherheitsbedrohungen geschützt werden können.

### <a name="soft-delete"></a>Vorläufiges Löschen
Das vorläufige Löschen von Dateifreigaben ist eine Einstellung auf Speicherkontoebene, die es Ihnen ermöglicht, Ihre Dateifreigabe wiederherzustellen, wenn diese versehentlich gelöscht wird. Wenn eine Dateifreigabe gelöscht wird, geht sie in einen vorläufig gelöschten Zustand über, anstatt dauerhaft gelöscht zu werden. Sie können den Zeitraum konfigurieren, in dem vorläufig gelöschte Daten wiederhergestellt werden können, ehe sie dauerhaft gelöscht werden, und die Löschung der Freigabe während dieses Aufbewahrungszeitraums jederzeit rückgängig machen. 

Es wird empfohlen, das vorläufige Löschen für die meisten Dateifreigaben zu aktivieren. Wenn Sie über einen Workflow verfügen, bei dem das Löschen von Dateifreigaben üblich und zu erwarten ist, können Sie sich für einen kurzen Aufbewahrungszeitraum oder die Deaktivierung der Funktion für das vorläufige Löschen entscheiden.

Weitere Informationen zum vorläufigen Löschen finden Sie unter [Verhindern eines versehentlichen Löschens von Azure-Dateifreigaben](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Sie können Ihre Azure-Dateifreigabe mithilfe von [Freigabemomentaufnahmen](./storage-snapshots-files.md) sichern, die schreibgeschützte Zeitpunktwiederherstellungskopien Ihrer Freigaben sind. Momentaufnahmen sind inkrementell, d. h., sie enthalten nur so viele Daten, wie seit der vorherigen Momentaufnahme geändert wurden. Sie können bis zu 200 Momentaufnahmen pro Dateifreigabe machen und diese bis zu zehn Jahre lang aufbewahren. Sie können die Momentaufnahmen entweder manuell im Azure-Portal, über PowerShell oder die Befehlszeilenschnittstelle (CLI) machen oder [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) verwenden. Momentaufnahmen werden in der Dateifreigabe gespeichert. Wenn Sie die Dateifreigabe also löschen, werden die Momentaufnahmen ebenfalls gelöscht. Stellen Sie sicher, dass das vorläufige Löschen für Ihre Dateifreigabe aktiviert ist, um Ihre Momentaufnahmensicherungen vor dem unbeabsichtigten Löschen zu schützen.

Im Artikel [Informationen zum Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) finden Sie Informationen zum Planen und Aufbewahren von Momentaufnahmen. Durch die Generationenprinzipfunktionen (grandfather-father-son, GFS) können Sie täglich, wöchentlich, monatlich oder jährlich Momentaufnahmen machen, die jeweils einen eigenen Aufbewahrungszeitraum haben. Azure Backup orchestriert außerdem die Aktivierung des vorläufigen Löschens und übernimmt eine Löschsperre für ein Speicherkonto, sobald eine beliebige Dateifreigabe für die Sicherung konfiguriert ist. Schließlich stellt Azure Backup bestimmte wichtige Überwachungs- und Warnungsfunktionen bereit, durch die Kunden eine konsolidierte Ansicht ihres Sicherungsbestands haben.

Mithilfe von Azure Backup können Sie im Azure-Portal Wiederherstellungen sowohl auf Element- als auch auf Freigabeebene durchführen. Sie müssen lediglich den Wiederherstellungspunkt (eine bestimmte Momentaufnahme), die bestimmte Datei oder das bestimmte Verzeichnis und dann den Speicherort (ursprünglich oder alternativ) auswählen, in dem die Ressourcen wiederhergestellt werden sollen. Der Sicherungsdienst übernimmt das Kopieren der Momentaufnahmedaten und zeigt den Wiederherstellungsfortschritt im Portal an.

Weitere Informationen zur Sicherung finden Sie unter [Informationen zum Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="protect-azure-files-with-microsoft-defender-for-storage"></a>Schützen von Azure Files mit Microsoft Defender für Speicher
Microsoft Defender für Storage bietet eine zusätzliche Ebene für sicherheitsbezogene Business Intelligence, die Warnungen generiert, wenn anomale Aktivitäten in Ihrem Speicherkonto erkannt werden, z. B. ungewöhnliche Zugriffsversuche. Es führt außerdem eine Analyse in Bezug auf die Malwarehashzuverlässigkeit durch und benachrichtigt Sie über bekannte Schadsoftware. Sie können Microsoft Defender für Speicher auf Abonnement- oder Speicherkontoebene über Microsoft Defender für Cloud konfigurieren.

Weitere Informationen finden Sie unter [Einführung in Microsoft Defender für Speicher ](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Speicherebenen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="limitations"></a>Einschränkungen
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundanz
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migration
In vielen Fällen werden Sie keine ganz neue Dateifreigabe für Ihre Organisation einrichten, sondern stattdessen eine vorhandene Dateifreigabe von einem lokalen Dateiserver oder NAS-Gerät zu Azure Files migrieren. Für den Erfolg der Migration ist es wichtig, die richtige Migrationsstrategie und das richtige Tool für Ihr Szenario auszuwählen. 

Der[Artikel zur Migrationsübersicht](storage-files-migration-overview.md) behandelt kurz die Grundlagen und enthält eine Tabelle, die Verweise auf Migrationsleitfäden enthält, die Ihr Szenario wahrscheinlich abdecken.

## <a name="next-steps"></a>Nächste Schritte
* [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md)
* [Bereitstellen von Azure Files](./storage-how-to-create-file-share.md)
* [Bereitstellen der Azure-Dateisynchronisierung](../file-sync/file-sync-deployment-guide.md)
* [Ermitteln des Migrationsleitfadens für Ihr Szenario anhand des Artikels zur Migrationsübersicht](storage-files-migration-overview.md)
