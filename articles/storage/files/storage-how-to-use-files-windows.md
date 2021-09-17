---
title: Einbinden einer Azure-SMB-Dateifreigabe unter Windows | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Dateifreigaben mit Windows und Windows Server verwenden. Verwenden Sie Azure-Dateifreigaben mit SMB 3.x in Windows-Installationen, die lokal oder auf Azure-VMs ausgeführt werden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8b469eb7eb94ad5454f79c4c4893597670867ac
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969507"
---
# <a name="mount-smb-azure-file-share-on-windows"></a>Einbinden einer Azure-SMB-Dateifreigabe unter Windows
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können in Windows und Windows Server nahtlos verwendet werden. In diesem Artikel werden die Überlegungen zur Verwendung einer Azure-Dateifreigabe mit Windows und Windows Server behandelt.

Wenn Sie eine Azure-Dateifreigabe über den öffentlichen Endpunkt außerhalb der Azure-Region verwenden möchten, in der sie gehostet wird (beispielsweise lokal oder in einer anderen Azure-Region), muss das Betriebssystem SMB 3.x unterstützen. Ältere Versionen von Windows, die nur SMB 2.1 unterstützen, können Azure-Dateifreigaben nicht über den öffentlichen Endpunkt einbinden.

| Windows-Version | SMB-Version | Azure Files SMB Multichannel | Maximale SMB-Kanalverschlüsselung |
|-|-|-|-|
| Windows Server 2022 | SMB 3.1.1 | Ja | AES-256-GCM |
| Windows 10, Version 21H1 | SMB 3.1.1 | Ja, mit KB5003690 oder neuer | AES-256-GCM |
| Windows Server, Version 20H2 | SMB 3.1.1 | Ja, mit KB5003690 oder neuer | AES-128-GCM |
| Windows 10, Version 20H2 | SMB 3.1.1 | Ja, mit KB5003690 oder neuer | AES-128-GCM |
| Windows Server, Version 2004 | SMB 3.1.1 | Ja, mit KB5003690 oder neuer | AES-128-GCM |
| Windows 10, Version 2004 | SMB 3.1.1 | Ja, mit KB5003690 oder neuer | AES-128-GCM |
| Windows Server 2019 | SMB 3.1.1 | Ja, mit KB5003703 oder neuer | AES-128-GCM |
| Windows 10, Version 1809 | SMB 3.1.1 | Ja, mit KB5003703 oder neuer | AES-128-GCM |
| Windows Server 2016 | SMB 3.1.1 | Ja, mit KB5004238 oder neuer | AES-128-GCM |
| Windows 10, Version 1607 | SMB 3.1.1 | Ja, mit KB5004238 oder neuer | AES-128-GCM |
| Windows 10, Version 1507 | SMB 3.1.1 | Ja, mit KB5004249 oder neuer | AES-128-GCM |
| Windows Server 2012 R2 | SMB 3.0 | Nein | AES-128-CCM |
| Windows 8.1 | SMB 3.0 | Nein | AES-128-CCM |
| Windows Server 2012 | SMB 3.0 | Nein | AES-128-CCM |
| Windows Server 2008 R2<sup>1</sup> | SMB 2.1 | Nein | Nicht unterstützt |
| Windows 7<sup>1</sup> | SMB 2.1 | Nein | Nicht unterstützt |

<sup>1</sup>Der reguläre Microsoft-Support für Windows 7 und Windows Server 2008 R2 wurde beendet. Es ist möglich, zusätzlichen Support nur für Sicherheitsupdates über das [ESU-Programm (Extended Security Update)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates) zu erwerben. Es wird dringend empfohlen, diese Betriebssysteme zu migrieren.

> [!Note]  
> Es empfiehlt sich, immer die neueste KB für Ihre Windows-Version zu verwenden.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Voraussetzungen 
Stellen Sie sicher, dass Port 445 geöffnet ist: Das SMB-Protokoll erfordert dass TCP-Port 445 geöffnet ist. Wenn Port 445 gesperrt ist, sind keine Verbindungen möglich. Mithilfe des Cmdlets `Test-NetConnection` können Sie überprüfen, ob Ihre Firewall Port 445 blockiert. Informationen zum Umgehen eines blockierten Ports 445 finden Sie im Abschnitt [Ursache 1: Port 445 ist blockiert](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) des Handbuchs zur Problembehandlung für Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Verwenden einer Azure-Dateifreigabe mit Windows
Zum Verwenden einer Azure-Dateifreigabe mit Windows müssen Sie sie entweder einbinden, d.h. ihr einen Laufwerkbuchstaben oder Bereitstellungspunktpfad zuweisen, oder über ihren [UNC-Pfad](/windows/win32/fileio/naming-a-file) darauf zugreifen. 

In diesem Artikel wird der Speicherkontoschlüssel für den Zugriff auf die Dateifreigabe verwendet. Ein Speicherkontoschlüssel ist ein Administratorschlüssel für ein Speicherkonto, der Administratorberechtigungen für alle Dateien und Ordner in der Dateifreigabe, auf die Sie zugreifen, sowie für alle Dateifreigaben und anderen Speicherressourcen (Blobs, Warteschlangen, Tabellen usw.) in Ihrem Speicherkonto umfasst. Wenn dies für Ihre Workload nicht ausreicht, können Sie [Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md) verwenden, oder Sie verwenden [identitätsbasierte Authentifizierung über SMB](storage-files-active-directory-overview.md).

Ein allgemeines Muster für das Migrieren von Branchenanwendungen per Lift & Shift, die eine SMB-Dateifreigabe an Azure erwarten, ist die Verwendung einer Azure-Dateifreigabe als Alternative für die Ausführung eines dedizierten Windows-Dateiservers auf einem virtuellen Azure-Computer. Ein wichtiger Aspekt für die erfolgreiche Migration einer Branchenanwendung zur Verwendung einer Azure-Dateifreigabe ist, dass viele Branchenanwendungen im Kontext eines dedizierten Dienstkontos mit begrenzten Berechtigungen anstatt des Administratorkontos eines virtuellen Computers ausgeführt werden. Daher müssen Sie sicherstellen, dass Sie die Anmeldeinformationen für die Azure-Dateifreigabe aus dem Kontext des Dienstkontos und nicht Ihres Administratorkontos einbinden/speichern.

### <a name="mount-the-azure-file-share"></a>Einbinden der Azure-Dateifreigabe

Das Azure-Portal stellt ein Skript bereit, mit dem Sie Ihre Dateifreigabe direkt auf einem Host einbinden können. Es wird empfohlen, das bereitgestellte Skript zu verwenden.

So erhalten Sie das Skript

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu dem Speicherkonto, das die Dateifreigabe enthält, die Sie einbinden möchten.
1. Klicken Sie auf **Dateifreigaben**.
1. Wählen Sie die Dateifreigabe aus, die Sie einbinden möchten.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Screenshot des Blatts „Dateifreigaben“, auf dem die Dateifreigabe hervorgehoben ist.":::

1. Wählen Sie **Verbinden**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Screenshot des Verbindungssymbols für die Dateifreigabe":::

1. Wählen Sie den Laufwerkbuchstaben aus, unter dem die Freigabe eingebunden werden soll.
1. Kopieren Sie das bereitgestellte Skript.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Screenshot des Blatts „Verbinden“, auf dem die Schaltfläche „Kopieren“ im Skript hervorgehoben ist.":::

1. Fügen Sie das Skript in eine Shell auf dem Host ein, auf dem Sie die Dateifreigabe einbinden möchten, und führen Sie es aus.

Sie haben nun ihre Azure-Dateifreigabe eingebunden.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Einbinden der Azure-Dateifreigabe über den Explorer
> [!Note]  
> Beachten Sie, dass sich die folgenden Anweisungen auf Windows 10 beziehen und bei älteren Versionen unter Umständen geringfügig unterscheiden können. 

1. Öffnen Sie den Datei-Explorer. Verwenden Sie dazu entweder das Startmenü, oder drücken Sie WINDOWS-TASTE+E.

1. Navigieren Sie im linken Fensterbereich zu **Dieser PC**. Dadurch ändern sich die verfügbaren Menüs auf dem Menüband. Wählen Sie im Menü „Computer“ die Option **Netzlaufwerk verbinden** aus.
    
    ![Screenshot: Dropdownmenü „Netzlaufwerk verbinden“](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Wählen Sie den Laufwerkbuchstaben aus, und geben Sie den UNC-Pfad im Format `\\<storageAccountName>.file.core.windows.net\<fileShareName>` ein. Beispiel: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Screenshot des Dialogfelds „Netzlaufwerk verbinden“](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Verwenden Sie den Speicherkontonamen mit vorangestelltem `AZURE\` als Benutzername und einen Speicherkontoschlüssel als Kennwort.
    
    ![Screenshot des Dialogfelds für die Netzwerkanmeldeinformationen](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Verwenden Sie die Azure-Dateifreigabe wie gewünscht.
    
    ![Die Azure-Dateifreigabe ist nun eingebunden.](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Wenn Sie die Einbindung der Azure-Dateifreigabe wieder aufheben möchten, klicken Sie im Explorer unter **Netzwerkadressen** mit der rechten Maustaste auf den Eintrag für die Freigabe, und wählen Sie **Trennen**.

### <a name="accessing-share-snapshots-from-windows"></a>Zugreifen auf Freigabemomentaufnahmen von Windows
Wenn Sie manuell oder automatisch über ein Skript oder einen Dienst wie Azure Backup eine Freigabemomentaufnahme erstellt haben, können Sie vorherige Versionen einer Freigabe, eines Verzeichnisses oder einer bestimmten Datei aus einer Dateifreigabe unter Windows anzeigen. Sie können eine Momentaufnahme mit [Azure PowerShell](storage-how-to-use-files-powershell.md), mit der [Azure CLI](storage-how-to-use-files-cli.md) oder über das [Azure-Portal](storage-how-to-use-files-portal.md) erstellen.

#### <a name="list-previous-versions"></a>Liste der vorherigen Versionen
Navigieren Sie zu dem Element oder übergeordneten Element, das wiederhergestellt werden soll. Doppelklicken Sie darauf, um zum gewünschten Verzeichnis zu navigieren. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü **Eigenschaften** aus.

![Kontextmenü für ein ausgewähltes Verzeichnis](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Wählen Sie **Vorherige Versionen** aus, um die Liste der Freigabemomentaufnahmen für dieses Verzeichnis anzuzeigen. Je nach Netzwerkgeschwindigkeit und Anzahl der Freigabemomentaufnahmen im Verzeichnis kann es einige Sekunden dauern, bis die Liste geladen ist.

![Registerkarte „Vorherige Versionen“](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Sie können **Öffnen** auswählen, um eine bestimmte Momentaufnahme zu öffnen. 

![Geöffnete Momentaufnahme](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Wiederherstellen aus einer vorherigen Version
Wählen Sie **Wiederherstellen**, um den Inhalt des gesamten Verzeichnisses rekursiv zum Erstellungszeitpunkt der Freigabemomentaufnahme am ursprünglichen Speicherort zu kopieren.

 ![Warnmeldung mit Schaltfläche „Wiederherstellen“](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files finden Sie unter diesen Links:
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Häufig gestellte Fragen](./storage-files-faq.md)
- [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Windows)