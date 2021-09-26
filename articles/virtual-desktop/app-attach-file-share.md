---
title: Einrichten einer Dateifreigabe für das MSIX-Feature zum Anfügen von Apps für Azure Virtual Desktop – Azure
description: Vorgehensweise beim Einrichten einer Dateifreigabe für das MSIX-Feature zum Anfügen von Apps für Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b568b11b7e0e630dcceef53e4c0f513dc0a7732c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547555"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Einrichten einer Dateifreigabe für das MSIX-Feature zum Anfügen von Apps

Alle MSIX-Images müssen auf einer Netzwerkfreigabe gespeichert werden, auf die Benutzer in einem Hostpool mit Leseberechtigung zugreifen können.

Das MSIX-Feature zum Anfügen von Apps ist nicht vom Typ des von der Dateifreigabe verwendeten Speicherfabrics abhängig. Für die Freigabe des MSIX-Features zum Anfügen von Apps gelten dieselben Überlegungen wie für eine FSLogix-Freigabe. Weitere Informationen zu Speicheranforderungen finden Sie unter [Speicheroptionen für FSLogix-Profilcontainer in Azure Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Leistungsanforderungen

Die Imagegrößenbeschränkungen Ihres Systems für das MSIX-Feature zum Anfügen von Apps hängen von dem Speichertyp ab, den Sie zum Speichern der VHD- oder VHDx-Dateien verwenden, sowie von den Größenbeschränkungen der VHD-, VHDX- oder CIM-Dateien und des Dateisystems.

In der folgenden Tabelle finden Sie ein Beispiel für den Ressourcenbedarf eines einzelnen 1-GB-MSIX-Images mit jeweils einer MSIX-App für jeden virtuellen Computer:

| Resource             | Anforderungen |
|----------------------|--------------|
| IOPS im stabilen Zustand    | 1 IOP       |
| Computerstartanmeldung | 10 IOPs      |
| Latency              | 400 ms       |

Die Anforderungen können stark variieren, je nachdem, wie viele MSIX-Paketanwendungen im MSIX-Image gespeichert werden. Für größere MSIX-Images müssen Sie mehr Bandbreite zuweisen.

### <a name="storage-recommendations"></a>Empfehlungen für Speicher

Azure bietet mehrere Speicheroptionen, die für das MSIX-Feature zum Anfügen von Apps verwendet werden können. Wir empfehlen die Verwendung von Azure Files oder Azure NetApp Files, da diese Optionen den besten Kompromiss zwischen Kosten und Verwaltungsaufwand bieten. Im Artikel [Speicheroptionen für FSLogix-Profilcontainer in Azure Virtual Desktop](store-fslogix-profile.md) werden die verschiedenen verwalteten Speicherlösungen verglichen, die Azure im Kontext von Azure Virtual Desktop bietet.

### <a name="optimize-msix-app-attach-performance"></a>Optimieren der Leistung des MSIX-Features zum Anfügen von Apps

Im Folgenden finden Sie weitere Empfehlungen zum Optimieren der Leistung des MSIX-Features zum Anfügen von Apps:

- Die Speicherlösung, die Sie für das MSIX-Feature zum Anfügen von Apps verwenden, sollte sich an demselben Rechenzentrumsstandort wie die Sitzungshosts befinden.
- Um Leistungsengpässe zu vermeiden, schließen Sie die folgenden VHD-, VHDX- und CIM-Dateien von Antivirenscans aus:
   
    - `<MSIXAppAttachFileShare\>\*.VHD`
    - `<MSIXAppAttachFileShare\>\*.VHDX`
    - `\\storageaccount.file.core.windows.net\share*.VHD`
    - `\\storageaccount.file.core.windows.net\share*.VHDX`
    - `<MSIXAppAttachFileShare>.CIM`
    - `\\storageaccount.file.core.windows.net\share**.CIM`

- Trennen Sie das Speicherfabric für das MSIX-Feature zum Anfügen von Apps von den FSLogix-Profilcontainern.
- Alle VM-Systemkonten und -Benutzerkonten müssen über Leseberechtigungen für den Zugriff auf die Dateifreigabe verfügen.
- Alle Pläne zur Notfallwiederherstellung für Azure Virtual Desktop müssen das Replizieren der Dateifreigabe für das MSIX-Feature zum Anfügen von Apps an den sekundären Failoverspeicherort beinhalten. Weitere Informationen zur Notfallwiederherstellung finden Sie unter [Einrichten eines Plans für Geschäftskontinuität und Notfallwiederherstellung](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Einrichten der Dateifreigabe

Der Setupprozess für die Dateifreigabe für das MSIX-Feature zum Anfügen von Apps ist größtenteils identisch mit dem [Setupprozess für FSLogix-Profildateifreigaben](create-host-pools-user-profile.md). Sie müssen den Benutzern jedoch unterschiedliche Berechtigungen zuweisen. Das MSIX-Feature zum Anfügen von Apps erfordert Leseberechtigungen für den Zugriff auf die Dateifreigabe.

Wenn Sie Ihre MSIX-Anwendungen in Azure Files speichern, müssen Sie für Ihre Sitzungshosts allen Sitzungshost-VMs sowohl die Berechtigungen der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für das Speicherkonto als auch NTFS-Berechtigungen (New Technology File System) für die Dateifreigabe auf der Freigabe zuweisen.

| Azure-Objekt                      | Erforderliche Rolle                                     | Rollenfunktion                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Sitzungshost (VM-Computerobjekte)| Speicherdateidaten-SMB-Freigabemitwirkender          | Lesen und ausführen, Lesen, Ordnerinhalt auflisten  |
| Administratoren in Dateifreigabe              | Speicherdateidaten-SMB-Freigabemitwirkender mit erhöhten Rechten | Vollzugriff                                  |
| Benutzer in Dateifreigabe               | Speicherdateidaten-SMB-Freigabemitwirkender          | Lesen und ausführen, Lesen, Ordnerinhalt auflisten  |

So weisen Sie Sitzungshost-VMs Berechtigungen für das Speicherkonto und die Dateifreigabe zu:

1. Erstellen Sie eine Active Directory Domain Services-Sicherheitsgruppe (AD DS).

2. Fügen Sie die Computerkonten für alle Sitzungshost-VMs als Mitglieder der Gruppe hinzu.

3. Synchronisieren Sie die AD DS-Gruppe mit Azure Active Directory (Azure AD).

4. Erstellen Sie ein Speicherkonto.

5. Erstellen Sie eine Dateifreigabe unter dem Speicherkonto, indem Sie die Anweisungen unter [Erstellen einer Azure-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) befolgen.

6. Fügen Sie das Speicherkonto in AD DS ein, indem Sie die Anweisungen in [Teil 1: Aktivieren der AD DS-Authentifizierung für Ihre Azure-Dateifreigaben](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) befolgen.

7. Weisen Sie die synchronisierte AD DS-Gruppe Azure AD zu, und weisen Sie das Speicherkonto der Rolle „Speicherdateidaten-SMB-Freigabemitwirkender“ zu.

8. Binden Sie die Dateifreigabe in einen beliebigen Sitzungshost ein, indem Sie die Anweisungen in [Teil 2: Zuweisen von Berechtigungen auf Freigabeebene für eine Identität](../storage/files/storage-files-identity-ad-ds-assign-permissions.md) befolgen.

9. Erteilen Sie der AD DS-Gruppe NTFS-Berechtigungen für die Dateifreigabe.

10. Richten Sie NTFS-Berechtigungen für die Benutzerkonten ein. Sie benötigen eine Betriebseinheit (OE), die aus der AD DS stammt, zu der die Konten auf dem virtuellen Computer gehören.

Nachdem Sie die Identität Ihrem Speicher zugewiesen haben, befolgen Sie die Anweisungen in den Artikeln in [Nächste Schritte](#next-steps), um der Identität, die Sie den VMs zugewiesen haben, andere erforderliche Berechtigungen zu erteilen.

Außerdem müssen Sie sicherstellen, dass Ihre Sitzungshost-VMs über NTFS-Berechtigungen verfügen. Sie müssen über einen Betriebseinheitencontainer verfügen, der aus Active Directory Domain Services (AD DS) stammt, und Ihre Benutzer müssen Mitglieder dieser Betriebseinheit sein, um diese Berechtigungen zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Einrichten der Dateifreigabe müssen Sie noch die folgenden Schritte ausführen:

- Unter [Erstellen eines Profilcontainers mit Azure Files und AD DS](create-file-share.md) erfahren Sie, wie Azure Active Directory Domain Services (AD DS) eingerichtet werden.
- Unter [Erstellen eines Profilcontainers mit Azure Files und AD DS](create-profile-container-adds.md) erfahren Sie, wie Azure Files und Azure AD DS eingerichtet werden.
- Unter [Erstellen eines Profilcontainers mit Azure NetApp Files und AD DS](create-fslogix-profile-container.md) erfahren Sie, wie Azure NetApp Files für das MSIX-Feature zum Anfügen von Apps eingerichtet wird.
- Unter [Erstellen eines Profilcontainers für einen Hostpool mit einer Dateifreigabe](create-host-pools-user-profile.md) erfahren Sie, wie eine Dateifreigabe auf VM-Basis verwendet wird.

Hier finden Sie einige weitere Ressourcen, die Ihnen möglicherweise nutzen:

- Stellen Sie unserer Community unter [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) Ihre Fragen zu dieser Funktion.
- Sie können Ihr Feedback bezüglich Azure Virtual Desktop auch im [Azure Virtual Desktop-Feedback-Hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) hochladen.
- [Glossar des MSIX-Features zum Anfügen von Apps](app-attach-glossary.md)
- [Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps](app-attach-faq.yml)
