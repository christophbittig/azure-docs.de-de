---
title: Problembehandlung bei Fehlschlägen der Freigabeverbindung während des Kopierens von Daten in eine Azure Data Box | Microsoft-Dokumentation
description: Beschreibt, wie man Netzwerkprobleme identifiziert, die SMB-Freigabeverbindungen während des Kopierens von Daten in eine Azure Data Box verhindern.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/23/2021
ms.author: alkohli
ms.openlocfilehash: afc76602b610488fd2ce4cf7f17e547821fc406c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773006"
---
# <a name="troubleshoot-share-connection-failure-during-data-copy-to-azure-data-box"></a>Problembehandlung bei Fehlschlägen der Freigabeverbindung während der Datenkopie in eine Azure Data Box

Dieser Artikel beschreibt, was zu tun ist, wenn Sie aufgrund eines Netzwerkproblems keine Verbindung zu einer SMB-Freigabe auf Ihrem Azure Data Box-Gerät herstellen können.

Die häufigsten Gründe dafür, dass Sie keine Verbindung zu einer Freigabe auf Ihrem Gerät herstellen können, sind:

- [ein Domänenproblem](#check-for-a-domain-issue)
- [Konto ist für die Freigabe gesperrt](#account-locked-out-of-share)
- [Gruppenrichtlinie verhindert eine Verbindung](#check-for-a-blocking-group-policy)
- [Berechtigungsissue](#check-for-permissions-issues)

## <a name="check-for-a-domain-issue"></a>Prüfen auf ein Domänenissue

So finden Sie heraus, ob ein Domänenissue eine Verbindung zur Freigabe verhindert:

- Versuchen Sie, eine Verbindung mit dem Gerät herzustellen, und verwenden Sie eines der folgenden Formate, um Ihren Benutzernamen einzugeben:

    - `<device IP address>\<user name>`
    - `\<user name>`

Wenn Sie eine Verbindung mit dem Gerät herstellen können, ist kein Domänenissue, das Ihre Verbindung mit der Freigabe verhindert.

## <a name="account-locked-out-of-share"></a>Konto für die Freigabe gesperrt

Nach fünf fehlgeschlagenen Versuchen, eine Verbindung zu einer Freigabe mit einem falschen Kennwort herzustellen, wird die Freigabe gesperrt, und Sie können 15 Minuten lang keine Verbindung herstellen.
 
Die fehlgeschlagenen Verbindungsversuche können Hintergrundprozesse wie Wiederholungsversuche beinhalten, die Sie möglicherweise nicht bemerken.

> [!NOTE]
> Wenn Sie ein älteres Gerät mit Data Box Version 4.0 oder früher haben, wird das Konto nach 3 fehlgeschlagenen Anmeldeversuchen für 30 Minuten gesperrt.

**Fehlerbeschreibung.** Je nachdem, wie Sie auf die Freigabe zugreifen, wird eine der folgenden Fehlermeldungen angezeigt:

- Wenn Sie versuchen, von Ihrem Host-Computer aus über SMB eine Verbindung herzustellen, wird dieser Fehler angezeigt: "Das betreffende Konto ist derzeit gesperrt und kann nicht angemeldet werden."

  Das folgende Beispiel zeigt die Ausgabe eines solchen Verbindungsversuchs.

  ```
  C:\Users\Databoxuser>net use \\10.100.100.10\mydbresources_BlockBlob /u:10.100.100.10\mydbresources
  Enter the password for '10.100.100.10\mydbresources' to connect to '10.100.100.10':
  System error 1909 has occurred.
  
  The referenced account is currently locked out and may not be logged on to.
  ```

- Wenn Sie den Datenkopierdienst verwenden, erhalten Sie in der lokalen Web-Benutzeroberfläche Ihres Geräts die folgende Meldung:

  ![Screenshot vom Bereich "Benachrichtigungen" auf der lokalen Weboberfläche für eine Data Box. Eine Benachrichtigung für ein gesperrtes Freigabekonto ist hervorgehoben.](media/data-box-troubleshoot-share-access/share-lock-01.png)


**Vorgeschlagene Lösung.** Um nach einer Freigabekontosperre eine Verbindung mit einer SMB-Freigabe herzustellen, gehen Sie folgendermaßen vor:

1. Überprüfen Sie die SMB-Anmeldeinformationen für die Freigabe. Gehen Sie in der lokalen Web-Benutzeroberfläche Ihres Geräts zu **Verbinden und Kopieren**, und wählen Sie **MBQ** für die Freigabe. Es wird das folgende Dialogfeld angezeigt.

    ![Screenshot vom Bildschirm „Freigabezugriff und Daten kopieren“ für eine SMB-Freigabe auf einer Data Box. Die Kopiersymbole für das Konto, den Benutzernamen und das Kennwort sind hervorgehoben.](media/data-box-troubleshoot-share-access/get-share-credentials-01.png)

1. Nach Ablauf der Sperrfrist (entweder 15 Minuten oder eine halbe Stunde) wird die Sperre aufgehoben. Sie können nun eine Verbindung mit der Freigabe herstellen.

   - Um eine Verbindung mit der Freigabe von Ihrem Host-Computer über SMB herzustellen, führen Sie den folgenden Befehl aus. Ein Verfahren dazu finden Sie unter [Kopieren von Daten zur Data Box via SMB](data-box-deploy-copy-data.md#connect-to-data-box).
  
     `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

   - Um eine Verbindung mit einer Freigabe über den Datenkopierdienst herzustellen, prüfen Sie, ob eine Benachrichtigung angezeigt wird, die besagt, dass das Benutzerkonto freigeschaltet wurde (siehe unten). Im Bereich **Daten kopieren** können Sie nun [Daten in die Data Box kopieren](data-box-deploy-copy-data-via-copy-service.md#copy-data-to-data-box).

     ![Screenshot des Bereichs "Daten kopieren" in der lokalen Web-Benutzeroberfläche der Data Box. Die Benachrichtigung, dass das Benutzerkonto für die Freigabe freigeschaltet wurde, und die Option "Daten kopieren" sind hervorgehoben.](media/data-box-troubleshoot-share-access/share-lock-02.png)


## <a name="check-for-a-blocking-group-policy"></a>Überprüfen auf eine Blockierungsgruppenrichtlinie

Überprüfen Sie, ob eine Gruppenrichtlinie auf Ihrem Client-/Hostcomputer verhindert, dass Sie eine Verbindung zu der Freigabe herstellen. Verschieben Sie Ihren Client-/Hostcomputer nach Möglichkeit in eine Organisationseinheit (OE), auf die keine Gruppenrichtlinie-Objekte (GPOs) angewendet wurden.

Um sicherzustellen, dass keine Gruppenrichtlinien Ihren Zugriff auf die Freigaben auf der Data Box verhindern:

* Vergewissern Sie sich, dass sich Ihr Client-/Host-Computer in seiner eigenen Organisationseinheit für Active Directory Domain Services befindet.

* Stellen Sie sicher, dass keine Gruppenrichtlinien auf Ihren Client-/Host-Computer angewendet werden. Sie können die Vererbung blockieren, um sicherzustellen, dass der Client/Host-Computer (untergeordneter Knoten) nicht automatisch irgendwelche GPOs vom übergeordneten Knoten erbt. Weitere Informationen finden Sie unter [Vererbung blockieren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

## <a name="check-for-permissions-issues"></a>Prüfen Sie auf Berechtigungsprobleme

Wenn es kein Domänenproblem gibt und keine Gruppenrichtlinien Ihren Zugriff auf die Freigabe blockieren, überprüfen Sie Ihr Gerät auf Berechtigungsprobleme, indem Sie die Prüfprotokolle und Sicherheitsereignisprotokolle überprüfen.

### <a name="review-security-event-logs"></a>Überprüfen von Sicherheitsereignisprotokollen

Überprüfen Sie die Windows-Sicherheitsereignisprotokolle auf dem Gerät auf Fehler, die auf einen Fehler bei der Authentifizierung hinweisen.

Sie können die `Smbserver.Security`Ereignisprotokolle im`etw` Ordner überprüfen oder Sicherheitsfehler in der Ereignisanzeige anzeigen.

Führen Sie die folgenden Schritte aus, um die Sicherheitsereignisprotokolle von Windows in der Ereignisanzeige zu überprüfen:

1. Um die Windows-Ereignisanzeige zu öffnen, geben Sie auf dem **Startbildschirm** **Event Viewer** ein und drücken Sie die Eingabetaste.

1. Im Navigationsbereich der Ereignisanzeige, erweitern Sie **Windows Protokolle**. und wählen Sie den Ordner **Security**.

    ![Screenshot der Windows-Ereignisanzeige mit angezeigten Sicherheitsereignissen. Der Ordner Windows und der Unterordner Security sind hervorgehoben.](media/data-box-troubleshoot-share-access/event-viewer-01.png)

3. Suchen Sie nach einem der folgenden Fehler:

    Fehler 1:

    ```xml
    SMB Session Authentication Failure
    Client Name: \\<ClientIP>
    Client Address: <ClientIP:Port>
    User Name:
    Session ID: 0x100000000021
    Status: The attempted logon is invalid. This is either due to a bad username or authentication information. (0xC000006D)
    SPN: session setup failed before the SPN could be queried
    SPN Validation Policy: SPN optional / no validation
    ```
      
    Fehler 2:
    ```xml
     LmCompatibilityLevel value is different from the default.
    Configured LM Compatibility Level: 5
    Default LM Compatibility Level: 3   
    ```

    Beide Fehler zeigen an, dass Sie die LAN-Manager-Authentifizierungsebene auf Ihrem Gerät ändern müssen.
 
### <a name="change-lan-manager-authentication-level"></a>Ändern der LAN-Manager-Authentifizierungsebene
 
Um die LAN-Manager-Authentifizierungsebene auf Ihrem Gerät zu ändern, können Sie entweder [die lokale Sicherheitsrichtlinie verwenden](#use-local-security-policy) oder [die Registrierung direkt aktualisieren](#update-the-registry).

#### <a name="use-local-security-policy"></a>Lokale Sicherheitsrichtlinie verwenden

Gehen Sie folgendermaßen vor, um die LAN Manager-Authentifizierungsebene mithilfe der lokalen Sicherheitsrichtlinie zu ändern:
 
1. Um die lokale Sicherheitsrichtlinie zu öffnen, geben Sie auf dem Bildschirm **Start** `secpol.msc` ein, und drücken Sie die Eingabetaste.

1. Gehen Sie zu **Lokale Richtlinien** > **Sicherheitsoptionen** und öffnen Sie **Netzsicherheit: LAN Manager-Authentifizierungsstufe**.

    ![Screenshot mit den Sicherheitsoptionen im Editor für lokale Sicherheitsrichtlinien. Die Richtlinie „Netzsicherheit: LAN-Manager-Authentifizierungsebene“ ist hervorgehoben.](media/data-box-troubleshoot-share-access/security-policy-01.png)

1. Ändern Sie die Einstellung auf **Nur NTLMv2-Antwort senden. LM & NTLMV2 Verweigern**.

    ![Screenshot mit der Richtlinie "Netzsicherheit: LAN-Manager-Authentifizierungsebene" im Editor für lokale Sicherheitsrichtlinien. Die Option "Nur NTLMv2-Antwort senden. LM & NTLM verweigern" ist hervorgehoben.](media/data-box-troubleshoot-share-access/security-policy-02.png)

#### <a name="update-the-registry"></a>Aktualisieren der Registrierung

Wenn Sie die LAN-Manager-Authentifizierungsebene in der lokalen Sicherheitsrichtlinie nicht ändern können, aktualisieren Sie die Registrierung direkt.

Um die Registrierung direkt zu aktualisieren, gehen Sie folgendermaßen vor:

1. Um den Registrierungseditor (regedit32.exe) zu öffnen, geben Sie auf dem **Start**-Bildschirm `regedt32` ein und drücken Sie die Eingabetaste.

1. Navigieren Sie zu: HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Steuerung > LSA.

    ![Screenshot des Registrierungseditors mit LSA-Ordner hervorgehoben.](media/data-box-troubleshoot-share-access/security-policy-03.png)

1. Öffnen Sie im Ordner LSA den Registrierungsschlüssel LMCompatibilityLevel und ändern Sie seinen Wert auf 5.

    ![Screenshot des Dialogfelds zum Ändern des Schlüssels LmcompatibilityLevel in der Registrierung. Das Datenfeld „Wert“ ist hervorgehoben.](media/data-box-troubleshoot-share-access/security-policy-04.png)

1. Starten Sie Ihren Computer neu, damit die Änderungen in der Registrierung wirksam werden.

## <a name="next-steps"></a>Nächste Schritte

- [Kopieren von Daten über SMB](data-box-deploy-copy-data.md).
- [Problemhandlung beim Kopieren von Daten in der Data Box](data-box-troubleshoot.md).
- [Kontaktieren Sie den Microsoft-Support ](data-box-disk-contact-microsoft-support.md).