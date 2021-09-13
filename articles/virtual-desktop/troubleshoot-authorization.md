---
title: Behandeln von Problemen mit Azure Files in Azure Virtual Desktop - Azure
description: Hier erfahren Sie mehr über das Behandeln von Problemen mit Azure Files in Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 31fe9f55252c00b5475bbb96cef646d0906a05f7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446820"
---
# <a name="troubleshoot-azure-files-authorization"></a>Problembehandlung bei der Azure Files-Autorisierung

In diesem Artikel werden häufige Probleme im Zusammenhang mit der Azure Files-Authentifizierung mit Azure Active Directory (AD) sowie Vorschläge zu deren Behebung beschrieben.

## <a name="my-group-membership-isnt-working"></a>Meine Gruppenmitgliedschaft funktioniert nicht.

Wenn Sie einen virtuellen Computer (VM) zu einer Active Directory Domain Services-Gruppe (AD DS) hinzufügen, müssen Sie den virtuellen Computer neu starten, um seine Mitgliedschaft im Dienst zu aktivieren.

## <a name="i-cant-add-my-storage-account-to-my-ad-ds"></a>Ich kann mein Speicherkonto nicht zu meinem AD DS hinzufügen.

Überprüfen Sie zunächst, ob Ihr Problem unter [Azure Files mit AD-Anmeldeinformationen kann nicht eingebunden werden](../storage/files/storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) aufgelistet ist.

Dies sind die häufigsten Ursachen für die Probleme:

- Ignorieren von Warnmeldungen, die beim Erstellen des Kontos in PowerShell angezeigt werden. Das Ignorieren von Warnungen kann dazu führen, dass die Einstellungen des neuen Kontos falsch konfiguriert sind. Um dieses Problem zu beheben, sollten Sie zunächst das Domänenkonto löschen, das das Speicherkonto darstellt, und den Vorgang dann wiederholen.

- Das Konto verwendet eine falsche Organisationseinheit (OE). Um dieses Problem zu beheben, geben Sie die OE-Informationen mit der folgenden Syntax erneut ein:
    
    ```powershell
    DC=ouname,DC=domainprefix,DC=topleveldomain
    ```

    Zum Beispiel:

    ```powershell
    DC=storageAccounts,DC=wvdcontoso,DC=com
    ```

- Machen Sie sich keine Sorgen, wenn das Speicherkonto nicht sofort in Ihrem Azure AD angezeigt wird. Es dauert in der Regel 30 Minuten, bis ein neues Speicherkonto mit dem Azure AD synchronisiert wird. Sie müssen also etwas Geduld aufbringen. Wenn die Synchronisierung nach 30 Minuten nicht abgeschlossen ist, lesen Sie [den nächsten Abschnitt](#my-ad-ds-group-wont-sync-to-azure-ad).

## <a name="my-ad-ds-group-wont-sync-to-azure-ad"></a>Meine AD DS-Gruppe wird nicht mit Azure AD synchronisiert.

Wenn Ihr Speicherkonto nicht automatisch nach 30 Minuten mit Azure AD synchronisiert wurde, müssen Sie die Synchronisierung mit [diesem Skript](https://github.com/stgeorgi/msixappattach/blob/master/force%20AD%20DS%20to%20Azure%20AD%20sync/force%20sync.ps1) erzwingen.

## <a name="my-storage-account-says-it-needs-additional-permissions"></a>Mein Speicherkonto fordert zusätzliche Berechtigungen.

Wenn Ihr Speicherkonto zusätzliche Berechtigungen benötigt, verfügen Sie möglicherweise nicht über die erforderliche Berechtigung für den Zugriff auf das MSIX-Feature zum Anfügen von Apps und auf FSLogix. Stellen Sie sicher, dass Sie Ihrem Konto eine der folgenden Berechtigungen zugewiesen haben, um dieses Problem zu beheben:

- Die RBAC-Berechtigung **Speicherdateidaten-SMB-Freigabemitwirkender**.

- Die NTFS-Berechtigungen **Lesen & Ausführen** und **Ordnerinhalt auflisten**.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich grundlegende Informationen über den Setupvorgang von Azure Files in Erinnerung rufen müssen, finden Sie diese unter [Autorisieren eines Kontos für Azure Files](azure-files-authorization.md).