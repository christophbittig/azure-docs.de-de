---
title: Massenlöschung von Benutzern im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Löschen Sie Benutzer in einem Massenvorgang im Azure Admin Center in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 07/09/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56822c8a350e1aaaa047b043e614b2eb20123e31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985426"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Massenlöschung von Benutzern in Azure Active Directory

Über das Azure Active Directory-Portal (Azure AD-Portal) können Sie eine große Anzahl von Mitgliedern aus einer Gruppe entfernen, indem Sie mithilfe einer CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) eine Massenlöschung von Benutzern ausführen.

## <a name="csv-template-structure"></a>CSV-Vorlagenstruktur

![Die CSV-Datei enthält Namen und IDs der zu löschenden Benutzer.](./media/users-bulk-delete/delete-csv-file.png)

Die Zeilen der heruntergeladenen CSV-Vorlage lauten wie folgt:

- **Versionsnummer**: Die erste Zeile, die die Versionsnummer enthält, muss in der hochzuladenden CSV-Datei enthalten sein.
- **Spaltenüberschriften**: `User name [userPrincipalName] Required`. Ältere Versionen der Vorlage können Abweichungen aufweisen.
- **Beispielzeile**: Wir haben in die Vorlage ein Beispiel für einen zulässigen Wert aufgenommen. `Example: chris@contoso.com` Sie müssen die Beispielzeile entfernen und durch Ihre eigenen Einträge ersetzen.

### <a name="additional-guidance"></a>Zusätzliche Anleitungen

- Die ersten beiden Zeilen der Vorlage dürfen nicht entfernt oder geändert werden, da die Vorlage dann nicht verarbeitet werden kann.
- Die erforderlichen Spalten werden zuerst aufgelistet.
- Fügen Sie der Vorlage keine neuen Spalten hinzu. Jegliche Spalten, die Sie zusätzlich hinzufügen, werden ignoriert und nicht verarbeitet.
- Laden Sie die neueste Version der CSV-Vorlage herunter, bevor Sie neue Änderungen vornehmen.

## <a name="to-bulk-delete-users"></a>So löschen Sie Benutzer in einem Massenvorgang

1. [Melden Sie sich bei ihrer Azure AD-Organisation](https://aad.portal.azure.com) mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Wählen Sie in Azure AD **Benutzer** > **Massenvorgänge** > **Massenlöschung** aus.
1. Wählen Sie auf der Seite zum **Massenlöschen von Benutzern** die Option **Download** aus, um die neueste Version der CSV-Vorlage herunterzuladen.
1. Öffnen Sie die CSV-Datei, und fügen Sie eine Zeile für jeden Benutzer hinzu, den Sie löschen möchten. Der einzige erforderliche Wert ist **Benutzerprinzipalname**. Speichern Sie die Datei .
1. Navigieren Sie auf der Seite **Massenlöschung von Benutzern** unter **CSV-Datei hochladen** zur entsprechenden Datei. Wenn Sie die Datei auswählen und auf „Senden“ klicken, wird mit der Überprüfung der CSV-Datei begonnen.
1. Nach der Überprüfung des Dateiinhalts wird die Meldung **Datei erfolgreich hochgeladen** angezeigt. Wenn Fehler vorliegen, müssen Sie diese beheben, bevor Sie den Auftrag übermitteln können.
1. Wenn Ihre Datei die Überprüfung bestanden hat, wählen Sie **Senden** aus, um den Azure-Massenvorgang zum Löschen der Benutzer zu starten.
1. Nach Abschluss des Löschvorgangs wird eine Benachrichtigung angezeigt, dass der Massenvorgang erfolgreich abgeschlossen wurde.

Wenn Fehler auftreten, können Sie die Ergebnisdatei auf der Seite **Ergebnisse von Massenvorgängen** herunterladen und anzeigen. Die Datei enthält den Grund für die einzelnen Fehler.

## <a name="check-status"></a>Status überprüfen

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

   [![Überprüfen des Löschstatus auf der Seite „Ergebnisse von Massenvorgängen“](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Als Nächstes können Sie entweder im Azure-Portal oder mithilfe von PowerShell überprüfen, ob die gelöschten Benutzer in der Azure AD-Organisation vorhanden sind.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Überprüfen gelöschter Benutzer im Azure-Portal

1. Melden Sie sich beim Azure-Portal mit einem Konto an, das über Benutzeradministratorberechtigungen in der Organisation verfügt.
1. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie unter **Anzeigen** nur **Alle Benutzer** aus, und vergewissern Sie sich, dass die gelöschten Benutzer nicht mehr aufgelistet werden.

### <a name="verify-deleted-users-with-powershell"></a>Überprüfen gelöschter Benutzer mit PowerShell

Führen Sie den folgenden Befehl aus:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Vergewissern Sie sich, dass die gelöschten Benutzer nicht mehr aufgelistet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Benutzer per Massenvorgang hinzufügen](users-bulk-add.md)
- [Herunterladen einer Benutzerliste](users-bulk-download.md)
- [Massenwiederherstellung von Benutzern](users-bulk-restore.md)
