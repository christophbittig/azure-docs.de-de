---
title: FSLogix-Profilcontainerdateien mit Azure Virtual Desktop – Azure
description: Dieser Artikel beschreibt FSLogix-Profilcontainer in Azure Virtual Desktop und Azure Files.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 93ef2ea1bcb10c08cfe6dc47027d12eeae3002b7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547483"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-Profilcontainer und Azure Files

Der Azure Virtual Desktop-Dienst empfiehlt FSLogix-Profilcontainer als Lösung für Benutzerprofile. FSLogix ist für das Roaming von Profilen in Remotecomputingumgebungen wie z B. Azure Virtual Desktop konzipiert. Das Feature speichert ein vollständiges Benutzerprofil in einem einzelnen Container. Bei der Anmeldung wird der Container dynamisch an die Computingumgebung angefügt. Hierzu werden nativ unterstützte VHD (Virtual Hard Disk) und VHDX (Hyper-V Virtual Hard Disk) verwendet. Das Benutzerprofil ist sofort verfügbar und wird im System genau so angezeigt wie ein natives Benutzerprofil. Dieser Artikel beschreibt, wie die mit Azure Files verwendeten FSLogix-Profilcontainer in Azure Virtual Desktop funktionieren.

> [!NOTE]
> Wenn Sie nach Vergleichsmaterial zu den verschiedenen Speicheroptionen des FSLogix-Profilcontainers in Azure suchen, lesen Sie [Speicheroptionen für FSLogix-Profilcontainer](store-fslogix-profile.md).

## <a name="user-profiles"></a>Benutzerprofile

Ein Benutzerprofil enthält Datenelemente über eine Person, einschließlich Konfigurationsinformationen wie Desktopeinstellungen, dauerhafte Netzwerkverbindungen und Anwendungseinstellungen. Windows erstellt standardmäßig ein lokales Benutzerprofil, das eng in das Betriebssystem integriert ist.

Ein Remotebenutzerprofil stellt eine Partition zwischen Benutzerdaten und Betriebssystem bereit. Es ermöglicht es, das Betriebssystem ohne Auswirkungen auf die Benutzerdaten zu ersetzen oder zu ändern. Bei Remotedesktop-Sitzungshosts (RDSH) und Infrastrukturen mit virtuellen Desktops (Virtual Desktop Infrastructures, VDI) können folgende Gründe für das Ersetzen des Betriebssystems vorliegen:

- Upgrade des Betriebssystems
- Austausch eines vorhandenen virtuellen Computers
- Benutzer als Teil einer in einem Pool zusammengefassten, nicht dauerhaften RDSH- oder VDI-Umgebung

Microsoft-Produkte arbeiten mit verschiedenen Technologien für Remotebenutzerprofile, wie z.B. den folgenden:
- Roamingbenutzerprofile (Roaming User Profiles, RUPs)
- Benutzerprofil-Datenträger (User Profile Disks, UPDs)
- Enterprise State Roaming (ESR)

UPD und RUP sind die am häufigsten eingesetzten Technologien für Benutzerprofile in RDSH- und VHD-Umgebungen.

### <a name="challenges-with-previous-user-profile-technologies"></a>Herausforderungen bei früheren Technologien für Benutzerprofile

Vorhandene und ältere Microsoft-Lösungen für Benutzerprofile bergen einige Herausforderungen. Keine bisherige Lösung kann alle Anforderungen an Benutzerprofile verarbeiten, die mit einer RDSH- oder VDI-Umgebung einhergehen. UPD beispielsweise kann keine großen OST-Dateien verarbeiten, und RUP speichert moderne Einstellungen nicht dauerhaft.

#### <a name="functionality"></a>Funktionalität

Die folgende Tabelle zeigt die Vorteile und Einschränkungen früherer Technologien für Benutzerprofile.

| Technologie | Moderne Einstellungen | Win32-Einstellungen | Betriebssystemeinstellungen | Benutzerdaten | In Server-SKU unterstützt | Back-End-Speicher in Azure | Back-End-Speicher in lokaler Umgebung | Versionsunterstützung | Zeitpunkt nachfolgender Anmeldungen |Notizen|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Benutzerprofil-Datenträger** | Ja | Ja | Ja | Ja | Ja | Nein | Ja | Win 7+ | Ja | |
| **Roamingbenutzerprofil, Wartungsmodus** | Nein | Ja | Ja | Ja | Ja| Nein | Ja | Win 7+ | Nein | |
| **Enterprise State Roaming** | Ja | Nein | Ja | Nein | Siehe Hinweise | Ja | Nein | Windows 10 | Nein | Funktioniert in Server-SKU, aber keine unterstützende Benutzeroberfläche. |
| **User Experience Virtualization (UE-V)** | Ja | Ja | Ja | Nein | Ja | Nein | Ja | Win 7+ | Nein |  |
| **OneDrive-Clouddateien** | Nein | Nein | Nein | Ja | Siehe Hinweise | Siehe Hinweise  | Siehe Hinweise | Win 10 RS3 | Nein | In Server-SKU nicht getestet. Back-End-Speicher in Azure hängt vom Synchronisierungsclient ab. Lokaler Back-End-Speicher benötigt einen Synchronisierungsclient. |

#### <a name="performance"></a>Leistung

UPD erfordert [Direkte Speicherplätze (Storage Spaces Direct, S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/), um Leistungsanforderungen zu erfüllen. UPD verwendet das Server Message Block-Protokoll (SMB). Die Technologie kopiert das Profil auf den virtuellen Computer, bei dem der Benutzer angemeldet ist.

#### <a name="cost"></a>Kosten

Die S2D-Cluster erzielen zwar die notwendige Leistung, aber die Kosten sind hoch – dies gilt für Enterprise-Kunden, aber ganz besonders für Kunden in kleinen und mittelgroße Unternehmen. Bei dieser Lösung zahlen die Unternehmen für Speicherdatenträger und müssen zudem die Kosten für die virtuellen Computer tragen, die die Datenträger für eine Freigabe nutzen.

#### <a name="administrative-overhead"></a>Verwaltungsaufwand

S2D-Cluster benötigen ein Betriebssystem, das gepatcht, aktualisiert und in einem sicheren Zustand gehalten wird. Aufgrund dieser Prozesse und der komplexen Einrichtung der S2D-Notfallwiederherstellung eignet sich S2D nur für Unternehmen mit dedizierten IT-Mitarbeitern.

## <a name="fslogix-profile-containers"></a>FSLogix-Profilcontainer

Am 19. November 2018 [erwarb Microsoft FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix löst viele Herausforderungen in Bezug auf Profilcontainer. Zu den wichtigsten zählen folgende:

- **Leistung:** [FSLogix-Profilcontainer](/fslogix/configure-profile-container-tutorial/) sind ausgesprochen leistungsfähig und lösen Leistungsprobleme, die bisher einen Austauschmodus mit Cache blockiert haben.
- **OneDrive:** Ohne FSLogix-Profilcontainer wird OneDrive for Business in nicht dauerhaften RDSH- und VDI-Umgebungen nicht unterstützt. Auf der [OneDrive-Seite für die Unterstützung von VDI](/onedrive/sync-vdi-support) erhalten Sie Informationen über die Interaktion. Weitere Informationen finden Sie unter [Verwenden des Synchronisierungsclients auf virtuellen Desktops](/deployoffice/rds-onedrive-business-vdi/).
- **Zusätzliche Ordner**: FSLogix bietet die Möglichkeit, Benutzerprofile auf zusätzliche Ordner zu erweitern.

Seit der Übernahme hat Microsoft damit begonnen, vorhandene Lösungen für Benutzerprofile wie UPD durch FSLogix-Profilcontainer zu ersetzen.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files-Integration im Azure Active Directory-Domänendienst

FSLogix-Profilcontainer profitieren durch Bereitstellung von Leistung und Features von der Cloud. Am 7. August 2019 hat Microsoft Azure Files die allgemeine Verfügbarkeit der [Azure Files-Authentifizierung mit Azure Active Directory Domain Services (Azure AD DS) ](../storage/files/storage-files-active-directory-overview.md) angekündigt. Die Kombination aus Azure Files und Azure AD DS-Authentifizierung reduziert sowohl die Kosten als auch den Verwaltungsaufwand – daher ist dies eine herausragende Lösung für Benutzerprofile im Azure Virtual Desktop-Dienst.

## <a name="best-practices-for-azure-virtual-desktop"></a>Best Practices für Azure Virtual Desktop

Azure Virtual Desktop bietet vollständige Kontrolle über Größe, Typ und Anzahl der von Kunden genutzten VMs. Weitere Informationen finden Sie unter [Was ist Azure Virtual Desktop?](overview.md).

Um sicherzustellen, dass die Azure Virtual Desktop-Umgebung den Best Practices entspricht, müssen folgende Voraussetzungen erfüllt sein:

- Das Azure Files-Speicherkonto muss sich in derselben Region wie die Sitzungshost-VMs befinden.
- Die Azure Files-Berechtigungen müssen den unter [Requirements – Profile Containers](/fslogix/fslogix-storage-config-ht) (Anforderungen – Profilcontainer) beschriebenen Berechtigungen entsprechen.
- Jede Hostpool-VM muss basierend auf dem gleichen Masterimage VMs des gleichen Typ und der gleichen Größe enthalten.
- Jede Hostpool-VM muss sich in der gleichen Ressourcengruppe befinden, um Verwaltung, Skalierung und Aktualisierung zu unterstützen.
- Um eine optimale Leistung zu erbringen, sollten sich die Speicherlösung und der FSLogix-Profilcontainer am gleichen Rechenzentrumsstandort befinden.
- Das Speicherkonto mit dem Masterimage muss sich in der gleichen Region und im gleichen Abonnement befinden, in der bzw. dem die VMs bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Gehen Sie folgendermaßen vor, um eine Azure Virtual Desktop-Umgebung einzurichten.

- Um mit dem Aufbau Ihrer Desktopvirtualisierungslösung zu beginnen, informieren Sie sich zunächst unter [Erstellen eines Mandanten in Azure Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- Informationen zum Erstellen eines Hostpools in Ihrem Azure Virtual Desktop-Mandanten finden Sie unter [Erstellen eines Hostpools mit Azure Marketplace](create-host-pools-azure-marketplace.md).
- Informationen zum Einrichten vollständig verwalteter Dateifreigaben in der Cloud finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung über SMB für Azure Files](/azure/storage/files/storage-files-active-directory-enable/).
- Informationen zum Konfigurieren von FSLogix-Profilcontainern finden Sie unter [Erstellen eines Profilcontainers für einen Hostpool unter Verwendung einer Dateifreigabe](create-host-pools-user-profile.md).
- Informationen zum Zuweisen von Benutzern zu einem Hostpool finden Sie unter [Verwalten von App-Gruppen für Azure Virtual Desktop](manage-app-groups.md).
- Informationen zum Zugriff auf Ihre Azure Virtual Desktop-Ressourcen über einen Browser finden Sie unter [Herstellen einer Verbindung mit Azure Virtual Desktop](./user-documentation/connect-web.md).
