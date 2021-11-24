---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 11/12/2021
ms.author: kengaderdus
ms.openlocfilehash: 957a119f1a48c1e79326d80a16763204cd41a669
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252485"
---
Wenn Sie noch nicht über ein Zertifikat verfügen, können Sie ein selbstsigniertes Zertifikat verwenden. Ein selbstsigniertes Zertifikat ist ein Sicherheitszertifikat, das nicht von einer Zertifizierungsstelle (ZS) signiert ist und nicht die Sicherheitsgarantien eines Zertifikats bietet, das von einer Zertifizierungsstelle signiert wurde. 

# <a name="windows"></a>[Windows](#tab/windows)

Verwenden Sie unter Windows das PowerShell-Cmdlet [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate), um ein Zertifikat zu generieren.

1. Führen Sie den folgenden PowerShell-Befehl aus, um ein selbstsigniertes Zertifikat zu generieren. Ändern Sie das Argument `-Subject` entsprechend Ihrer Anwendung und dem Azure AD B2C-Mandantennamen, z. B. `contosowebapp.contoso.onmicrosoft.com`. Sie können auch das `-NotAfter`-Datum anpassen, um einen anderen Ablaufzeitpunkt für das Zertifikat anzugeben.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Suchen Sie auf dem Windows-Computer nach **Benutzerzertifikate verwalten** und wählen Sie 
1. Wählen Sie unter **Zertifikate - Aktueller Benutzer** die Option **Persönlich** > **Zertifikate**>*IhrAnwendungsname.ihrmieter.onmicrosoft.com*.
1. Wählen Sie das Zertifikat und dann **Aktion** > **Alle Aufgaben** > **Exportieren** aus.
1. Wählen Sie **Weiter** > **Ja, den privaten Schlüssel exportieren** > **Weiter**.
1. Übernehmen Sie die Standardeinstellungen für **Exportdateiformat**, und wählen Sie dann **Weiter**.
1. Aktivieren Sie die Option **Passwort**, geben Sie ein Passwort für das Zertifikat ein, und wählen Sie dann **Weiter**.
1. Um einen Speicherort für Ihr Zertifikat anzugeben, wählen Sie **Durchsuchen** und navigieren Sie zu einem Verzeichnis Ihrer Wahl. 
1. Geben Sie im Fenster **Speichern unter** einen **Dateinamen** ein, und wählen Sie dann **Speichern**.
1. Wählen Sie **Weiter**>**Fertig stellen** aus.

Damit das Kennwort für die PFX-Datei in Azure AD B2C akzeptiert wird, muss es statt mit „AES256-SHA256“ mit der Option „TripleDES-SHA1“ im Exporthilfsprogramm des Windows-Zertifikatspeichers verschlüsselt werden.

# <a name="macos"></a>[macOS](#tab/macos)

Verwenden Sie unter macOS den [Zertifikatsassistenten](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) in Keychain Access, um ein Zertifikat zu generieren.

1. Führen Sie die Anweisungen unter [Erstellen von selbstsignierten Zertifikaten mit der Schlüsselbundverwaltung auf dem Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac) aus.
1. Wählen Sie in der App „Schlüsselbundverwaltung“ auf Ihrem Mac das von Ihnen erstellte Zertifikat aus.
1. Wählen Sie **Ablage** > **Objekte exportieren** aus.
1. Wählen Sie einen Dateinamen aus, um das Zertifikat zu speichern. Beispiel: **self-signed-certificate.p12**.
1. Wählen Sie als **Dateiformat** die Option **Personal Information Exchange (.p12)** aus.
1. Wählen Sie **Speichern** aus.
1. Geben Sie ein Kennwort in den Feldern **Kennwort** und **Bestätigen** ein.
1. Ändern Sie die Erweiterung der Datei in „.pfx“. Beispiel: **self-signed-certificate.pfx**.

---
