---
title: 'Tutorial: Ausstellen und Überprüfen von Nachweisen mithilfe Ihres Azure-Mandanten (Vorschau)'
description: Ändern des Nachweiscodebeispiels für Ihren Azure-Mandanten
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: barclayn
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: bf1e79f2868ee5f1a3c6b0b920a93a8a1f92c068
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049157"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Tutorial: Ausstellen und Überprüfen von Nachweisen mithilfe Ihres Mandanten (Vorschau)

> [!IMPORTANT]
> Überprüfbare Anmeldeinformationen für Azure Active Directory-befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nachdem Sie Ihren Azure-Mandanten mit dem Nachweisdienst eingerichtet haben, führen Sie die Schritte aus, die erforderlich sind, damit Azure Active Directory (Azure AD) Nachweise über die Beispiel-App ausstellen kann.

In diesem Artikel führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Registrieren der Beispiel-App in Ihrer Azure AD-Instanz
> * Erstellen von Regel- und Anzeigedateien
> * Hochladen von Regel- und Anzeigedateien
> * Einrichten des Nachweisausstellerdiensts für die Verwendung von Azure Key Vault
> * Aktualisieren des Beispielcodes mit den Informationen Ihres Mandanten

Für unseren Beispielcode ist die Authentifizierung von Benutzern bei einem Identitätsanbieter, namentlich Azure AD B2C, erforderlich, damit der Nachweis für verifizierte Anmeldeinformationsexperten ausgestellt werden kann. Nicht alle Nachweisaussteller erfordern eine Authentifizierung, bevor sie Anmeldeinformationen ausstellen.

Durch das Authentifizieren von ID-Token können Benutzer ihre Identität nachweisen, bevor sie ihre Anmeldeinformationen erhalten. Wenn sich der Benutzer erfolgreich angemeldet hat, gibt der Identitätsanbieter ein Sicherheitstoken zurück, das Ansprüche für den Benutzer enthält. Der Ausstellerdienst wandelt diese Sicherheitstoken und die zugehörigen Ansprüche dann in einen Nachweis um. Der Nachweis wird mit der DID des Ausstellers signiert.

Alle Identitätsanbieter mit Unterstützung des OpenID Connect-Protokolls werden unterstützt. Beispiele für unterstützte Identitätsanbieter sind [Azure Active Directory](../fundamentals/active-directory-whatis.md) und [Azure AD B2C](../../active-directory-b2c/overview.md). In diesem Tutorial verwenden wir AAD.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass Sie bereits die Schritte des [vorherigen Tutorials](enable-your-tenant-verifiable-credentials.md) ausgeführt haben und auf die dort verwendete Umgebung zugreifen können.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Registrieren einer App zum Erlauben von DID-Wallets für die Benutzeranmeldung

Um einen Nachweis auszustellen, müssen Sie eine App registrieren, damit Benutzer über Authenticator oder ein anderes Nachweis-Wallet angemeldet werden können.  

Registrieren Sie eine Anwendung namens „Nachweis-Wallet-App“ in Azure AD, und erhalten Sie eine Client-ID.

1. Befolgen Sie die Anweisungen zum Registrieren einer Anwendung bei [Azure AD](../develop/quickstart-register-app.md). Verwenden Sie bei der Registrierung die unten aufgeführten Werte.

   - Name: „Nachweis-Wallet-App“
   - Unterstützte Kontotypen: Nur Konten in diesem Organisationsverzeichnis
   - Umleitungs-URI: vcclient://openid/

   ![Registrieren einer Anwendung](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Nachdem Sie die Anwendung registriert haben, notieren Sie sich die Anwendungs-ID (Client). Sie benötigen diesen Wert später noch.

   ![Anwendungs-ID (Client)](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Klicken Sie auf die Schaltfläche **Endpunkte**, und kopieren Sie den URI zum OpenID Connect-Metadatendokument. Sie benötigen diese Informationen für den nächsten Abschnitt. 

   ![Ausstellerendpunkte](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)


Bisher haben wir mit unserer Beispiel-App gearbeitet. Diese App verwendet [Azure Active Directory B2C](../../active-directory-b2c/overview.md). Jetzt möchten wir stattdessen Azure AD verwenden und müssen daher einige Änderungen vornehmen – nicht nur zur Anpassung an Ihre Umgebung, sondern auch zur Unterstützung zusätzlicher Ansprüche, die zuvor noch nicht verwendet wurden.

1. Kopieren Sie die unten aufgeführte Regeldatei, und speichern Sie sie in **modified-expertRules.json**. 

    > [!NOTE]
    > **"scope": "openid profile"** ist in dieser Regeldatei, aber nicht in der Regeldatei der Beispiel-App enthalten. Im nächsten Abschnitt wird erläutert, wie Sie die optionalen Ansprüche in Ihrem Azure Active Directory-Mandanten aktivieren. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Öffnen Sie die Datei, und ersetzen Sie die Werte für **client_id** und **configuration** durch die beiden Werte, die wir im vorherigen Abschnitt kopiert haben.

    ![Hervorhebung der beiden Werte, die im Rahmen dieses Schritts geändert werden müssen](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Der Wert **configuration** ist der URI für das OpenID Connect-Metadatendokument.

  Da im Beispielcode Azure Active Directory B2C verwendet wird, wir jedoch Azure Active Directory verwenden, müssen wir optionale Ansprüche über Bereiche hinzufügen, damit diese Ansprüche in das ID-Token eingeschlossen werden, das in den Nachweis geschrieben werden soll. 

3. Öffnen Sie Azure Active Directory im Azure-Portal.
4. Wählen Sie **App-Registrierungen** aus.
5. Öffnen Sie die zuvor erstellte Nachweis-Wallet-App.
6. Wählen Sie **Tokenkonfiguration** aus.
7. Wählen Sie **+ Optionalen Anspruch hinzufügen** aus.

     ![Hinzufügen eines optionalen Anspruchs unter „Tokenkonfiguration“](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Wählen Sie unter **Tokentyp** die Option **ID** aus, und wählen Sie aus der Liste der verfügbaren Ansprüche **given_name** und **family_name** aus.

     ![Hinzufügen optionaler Ansprüche](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Klicken Sie auf **Hinzufügen**.
10. Wenn Sie wie unten gezeigt eine Berechtigungswarnung erhalten, aktivieren Sie das Kontrollkästchen, und klicken Sie auf **Hinzufügen**.

     ![Hinzufügen von Berechtigungen für optionale Ansprüche](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Wenn nun einem Benutzer das Anmeldedialogfeld zur Ausstellung Ihres Nachweises angezeigt wird, schließt die Nachweis-Wallet-App die spezifischen Ansprüche über den Bereichsparameter ein und schreibt sie in den Nachweis.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Erstellen eines neuen Nachweises mit dieser Regeldatei und der alten Anzeigedatei

1. Laden Sie die neue Regeldatei in den Container hoch.
1. Erstellen Sie auf der Nachweisseite mithilfe der alten Anzeigedatei und der neuen Regeldatei (**modified-credentialExpert.json**) eine neue Anmeldeinformation namens **modified-credentialExpert.json**.
1. Nachdem der Vorgang zum Erstellen der Anmeldeinformation auf der Seite **Übersicht** abgeschlossen wurde, kopieren Sie die **URL für das Ausstellen von Anmeldeinformationen**, und speichern Sie sie, da wir sie im nächsten Abschnitt benötigen.

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Einrichten der Node-App mit Zugriff auf Azure Key Vault

Zum Authentifizieren einer Benutzeranforderung zum Ausstellung von Anmeldeinformationen verwendet die Ausstellerwebsite Ihre kryptografischen Schlüssel in Azure Key Vault. Für den Zugriff auf Azure Key Vault benötigt Ihre Website eine Client-ID und einen geheimen Clientschlüssel zur Authentifizierung.

Zuerst müssen Sie eine andere Anwendung registrieren. Diese Registrierung gilt für die Website. Die zuvor erfolgte Registrierung für die Wallet-Anwendung soll Benutzern lediglich das Anmelden beim Verzeichnis mit der Wallet-Anwendung ermöglichen. In unserem Fall befindet sie sich im selben Verzeichnis, aber die Registrierung der Wallet-Anwendung hätte auch in einem anderen Verzeichnis erfolgen können. Eine Best Practice besteht im Verwenden getrennter Anwendungsregistrierungen, wenn die Verantwortung der Anwendungen unterschiedlich ist. In diesem Fall müssen Sie Ihre Website verwenden, um auf Key Vault zuzugreifen.

1. Befolgen Sie die Anweisungen zum Registrieren einer Anwendung bei [Azure AD](../develop/quickstart-register-app.md). Verwenden Sie bei der Registrierung die unten aufgeführten Werte.

   - Name: „VC-Website“
   - Unterstützte Kontotypen: Nur Konten in diesem Organisationsverzeichnis

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-registration.png" alt-text="Screenshot: Registrieren einer Anwendung":::

1. Nachdem Sie die Anwendung registriert haben, notieren Sie sich die Anwendungs-ID (Client). Sie benötigen diesen Wert später noch.

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-details.png" alt-text="Screenshot: Client-ID der Anwendung":::

1. Wählen Sie auf der Übersicht der „VC Website“-Anwendung die Option **Zertifikate & Geheimnisse** aus.

    :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-certificates-secrets.png" alt-text="Screenshot: der Bereich „Zertifikate und Geheimnisse“":::

1. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus.
    1. Fügen Sie eine Beschreibung hinzu, zum Beispiel „Geheimer Clientschlüssel für Node-Nachweis“.
    1. Läuft ab: In einem Jahr.

    ![Anwendungsgeheimnis mit einer Ablaufzeit von einem Jahr](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)

1. Kopieren Sie das Geheimnis. Sie benötigen diese Informationen, um Ihre Beispiel-Node-App zu aktualisieren.

>[!WARNING]
> Dies ist die einzige Möglichkeit, das Geheimnis zu kopieren. Anschließend wird es durch eine Einweghashfunktion verschlüsselt. Kopieren Sie nicht die ID. 

Nachdem Sie Ihre Anwendung und den geheimen Clientschlüssel in Azure AD erstellt haben, müssen Sie der Anwendung die erforderlichen Berechtigungen zum Ausführen von Vorgängen für Ihren Schlüsseltresor erteilen. Diese Berechtigungsänderungen sind erforderlich, damit die Website auf die dort gespeicherten privaten Schlüssel zugreifen und diese verwenden kann.

1. Wechseln Sie zu Key Vault.
2. Wählen Sie den Schlüsseltresor aus, den wir für diese Tutorials verwenden.
3. Wählen Sie im linken Navigationsbereich **Zugriffsrichtlinien** aus.
4. Wählen Sie **+ Zugriffsrichtlinie hinzufügen** aus.
5. Wählen Sie im Abschnitt **Schlüsselberechtigungen** die Optionen **Abrufen** und **Signieren** aus.
6. Wählen Sie **Prinzipal** aus, und suchen Sie anhand der Anwendungs-ID nach der zuvor registrierten Anwendung. Wählen Sie ihn aus.
7. Wählen Sie **Hinzufügen**.
8. Wählen Sie **SPEICHERN** aus.

:::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png" alt-text="Screenshot: Hinzufügen einer Zugriffsrichtlinie":::

Weitere Informationen zu Key Vault-Berechtigungen und zur Zugriffssteuerung finden Sie im [Leitfaden zu RBAC für Key Vault](../../key-vault/general/rbac-guide.md).

## <a name="make-changes-to-the-sample-app"></a>Nehmen Sie die erforderlichen Änderungen an der Beispiel-Anwendung vor.

Wir müssen einige Werte zusammenstellen, bevor wir die erforderlichen Codeänderungen vornehmen können. Diese Werte werden im nächsten Abschnitt eingesetzt, damit der Beispielcode Ihre eigenen, im Tresor gespeicherten Schlüssel verwendet. Bisher stehen die folgenden Werte bereit.

- **Vertrags-URI** aus der soeben erstellten Anmeldeinformation (URL für das Ausstellen von Anmeldeinformationen)
- **Anwendungs Client-ID**, die wir bei der Registrierung der Node-App erhalten haben
- **Geheimer Clientschlüssel**, den wir erstellt haben, um Ihrer App Zugriff auf den Schlüsseltresor zu erteilen

Es gibt noch einige weitere Werte, die erforderlich sind, um die Änderungen einmalig in unserer Beispiel-App vornehmen zu können. Diese werden im Folgenden ermittelt.

### <a name="verifiable-credentials-settings"></a>Nachweiseinstellungen

1. Navigieren Sie zur Seite „Nachweise“, und wählen Sie **Einstellungen** aus.  
1. Kopieren Sie die folgenden Werte:

    - Mandanten-ID 
    - Ausstellerbezeichner (Ihre DID)
    - Schlüsseltresor (URI)

1. Unter dem Signaturschlüsselbezeichner ist ein URI aufgeführt, aber wir benötigen nur einen Teil davon. Kopieren Sie ab dem Teil mit dem Text **issuerSigningKeyION**, wie durch das rote Rechteck in der folgenden Abbildung hervorgehoben.

   ![Anmeldeschlüsselbezeichner](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID-Dokument

1. Öffnen Sie den [DID-ION-Netzwerk-Explorer](https://identity.foundation/ion/explorer/).

2. Fügen Sie Ihre DID in die Suchleiste ein.

4. Suchen Sie in der formatierten Antwort den Abschnitt **verificationMethod**.
5. Kopieren Sie die `id` unter „verificationMethod“, und bezeichnen Sie sie als „kvSigningKeyId“.
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Jetzt haben wir alles, was wir brauchen, um die Änderungen an unserem Beispielcode vorzunehmen.

- **Aussteller:** app.js const-Anmeldeinformationen mit Ihrem neuen Vertrags-URI aktualisieren
- **Überprüfung:** „app.js“, aktualisieren Sie „issuerDid“ mit Ihrem Ausstellerbezeichner.
- **Aussteller und Überprüfung:** Aktualisieren Sie „didconfig.json“ mit den folgenden Werten:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Dies ist eine Demoanwendung. Normalerweise sollten Sie das Geheimnis niemals direkt an Ihre Anwendung übergeben.


Jetzt steht alles bereit, um Ihren eigenen Nachweis über Ihren Azure Active Directory-Mandanten mit Ihren eigenen Schlüsseln auszustellen und zu überprüfen. 

## <a name="issue-and-verify-the-vc"></a>Ausstellen und Überprüfen des Nachweises

Führen Sie dieselben Schritte wie im vorherigen Tutorial aus, um den Nachweis auszustellen und mit Ihrer App zu überprüfen. Nachdem Sie den Überprüfungsprozess erfolgreich abgeschlossen haben, können Sie mehr über Nachweise erfahren.

1. Öffnen Sie eine Eingabeaufforderung und navigieren Sie zum Ordner .
2. Führen Sie das aktualisierte Skript aus.

    ```terminal
    node app.js
    ```

3. Führen Sie mit einer anderen Eingabeaufforderung ngrok aus, um eine URL auf 8081 einzurichten

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Möglicherweise bemerken Sie auch eine Warnung, dass diese APP oder Website riskant sein kann. Die Meldung wird zu diesem Zeitpunkt erwartet, da Ihr DID noch nicht mit Ihrer Domäne verknüpft wurde. Befolgen Sie die Anweisungen der [DNS-Bindung](how-to-dnsbind.md), um dies zu konfigurieren.

    
4. Öffnen Sie die HTTPS-URL, die von ngrok generiert wird.

    ![Ngrok-Weiterleitungs Endpunkte](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Wählen Sie  **ANMELDEINFORMATIONEN ERHALTEN**
6. Scannen Sie in den Authentifikator den QR-Code.
7. Bei **dieser APP oder Website ist möglicherweise eine riskant** Warnmeldung, wählen Sie **erweitert** aus.

  ![Anfängliche Warnung](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Wählen Sie unter der Warnung riskante Website die Option **trotzdem fortsetzen (unsicher)**

  ![Zweite Warnung zum Aussteller](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Beachten Sie auf dem Bildschirm **Anmeldeinformationen Hinzufügen** einige Dinge: 
    1. Am oberen Bildschirmrand sehen Sie eine rote **nicht verifizierte** Meldung
    1. Die Anmeldeinformationen werden auf der Grundlage der Änderungen angepasst, die wir an der Anzeige Datei vorgenommen haben.
    1. Die Option **Bei Ihrem Konto anmelden** verweist auf Ihre Azure AD-Anmeldeseite.
    
   ![Bildschirm-Anmeldeinformationen mit Warnung hinzufügen](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Wählen Sie **Bei Ihrem Konto anmelden** aus, und authentifizieren Sie sich anhand eines Benutzers im Azure AD-Mandanten.
11. Nach der erfolgreichen Authentifizierung ist die Schaltfläche **Hinzufügen** nicht mehr abgeblendet. Wählen Sie **Hinzufügen**.

  ![Bildschirm-Anmeldeinformationen nach der Authentifizierung hinzufügen](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Sie haben jetzt einen Nachweis mit Ihrem Mandanten zum Generieren des Nachweises ausgestellt, während Sie weiterhin den ursprünglichen B2C-Mandanten für die Authentifizierung verwenden.

  ![VC ausgestellt von Ihrer Azure AD und authentifiziert von unserer Azure B2C-Instanz](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testen der VC-Überprüfung mithilfe der Beispiel-App

Nachdem Sie nun den Nachweis aus Ihrem eigenen Mandanten mit Ansprüchen aus Ihrer Azure AD-Instanz ausgestellt haben, überprüfen Sie ihn mit der Beispiel-App.

1. Starten Sie den ngrok-Dienst für den Aussteller nicht mehr.

    ```cmd
    control-c
    ```

2. Führen Sie nun ngrok mit dem Überprüfungs-Port 8082 aus.

    ```cmd
    ngrok http 8082
    ```

3. Navigieren Sie in einem anderen Terminalfenster zur Verifizierzungs-App, und führen Sie es ähnlich wie wir die Aussteller-App ausgeführt haben.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Öffnen Sie die ngrok-URL in Ihrem Browser, und scannen Sie den QR-Code mithilfe von Authenticator auf Ihrem mobilen Gerät.
5. Wählen Sie auf Ihrem mobilen Gerät auf dem Bildschirm die **neue Berechtigungsanforderung** Option **zulassen** aus.

   >[!IMPORTANT]
    > Da die Beispiel-App auch Ihre DID zum Signieren der Präsentationsanforderung verwendet, wird eine Warnung angezeigt, dass diese App oder Website riskant sein kann. Die Meldung wird zu diesem Zeitpunkt erwartet, da Ihr DID noch nicht mit Ihrer Domäne verknüpft wurde. Befolgen Sie die Anweisungen der [DNS-Bindung](how-to-dnsbind.md), um dies zu konfigurieren.
    
   ![Neue Berechtigungsanforderung](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Sie haben ihre Anmeldeinformation erfolgreich überprüft, und auf der Website sollten Ihr Vor- und Nachname aus Ihrem Azure AD-Benutzerkonto angezeigt werden. 

Sie haben das Tutorial abgeschlossen und sind offiziell ein Experte für Nachweise! Ihre Beispiel-App verwendet Ihre DID zum Ausstellen und Überprüfen, während Ansprüche in einen Nachweis aus Ihrer Azure AD-Instanz geschrieben werden. 

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen [benutzerdefinierter Anmeldeinformationen](credential-design.md)
- [Beispiele](issuer-openid.md) für die Kommunikation des Ausstellerdiensts
