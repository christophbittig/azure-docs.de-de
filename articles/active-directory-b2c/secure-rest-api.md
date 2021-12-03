---
title: Schützen von APIs, die als API-Connectors in Azure AD B2C verwendet werden
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie Ihre benutzerdefinierten RESTful-APIs schützen, die als API-Connectors in Azure AD B2C verwendet werden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a738459e50ed87dbfbdc97838d70f049d998eca5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326802"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-b2c"></a>Schützen Ihrer API, die als API-Connector in Azure AD B2C verwendet wird 

Wenn Sie eine REST-API in einen Azure AD B2C-Benutzerflow integrieren, müssen Sie Ihren REST-API-Endpunkt mittels Authentifizierung schützen. Durch diese REST-API-Authentifizierung wird sichergestellt, dass nur Dienste mit richtigen Anmeldeinformationen, wie z. B. Azure AD B2C, Aufrufe an Ihren Endpunkt tätigen können. In diesem Artikel wird erläutert, wie die REST-API geschützt wird. 


## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte im Leitfaden [Exemplarische Vorgehensweise: Hinzufügen eines API-Connectors zu einem Benutzerflow für die Registrierung](add-api-connector.md) aus.

::: zone pivot="b2c-user-flow"

Sie können Ihren API-Endpunkt entweder mithilfe der HTTP-Standardauthentifizierung oder mithilfe der HTTPS-Clientzertifikatauthentifizierung schützen. In beiden Fällen müssen Sie die Anmeldeinformationen angeben, die Azure AD B2C beim Aufrufen Ihres API-Endpunkts verwenden wird. Der API-Endpunkt überprüft dann die Anmeldeinformationen und führt Autorisierungsentscheidungen aus.

::: zone-end

## <a name="http-basic-authentication"></a>HTTP-Standardauthentifizierung

Die HTTP-Standardauthentifizierung ist in [RFC 2617](https://tools.ietf.org/html/rfc2617) definiert. Die Standardauthentifizierung funktioniert wie folgt: Von Azure AD B2C wird eine HTTP-Anforderung mit den Clientanmeldeinformationen (`username` und `password`) im Header `Authorization` gesendet. Die Anmeldeinformationen werden als Base64-codierte Zeichenfolge im Format `username:password` angegeben. Ihre API ist dann für die Überprüfung dieser Werte verantwortlich, um andere Autorisierungsentscheidungen zu treffen.

::: zone pivot="b2c-user-flow"

Führen Sie die folgenden Schritte aus, um einen API-Connector mit HTTP-Standardauthentifizierung zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus, oder suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie diesen aus.
3. Wählen Sie **API-Connectors** und dann den zu konfigurierenden **API-Connector** aus.
4. Wählen Sie als **Authentifizierungstyp** die Option **Standard** aus.
5. Geben Sie den **Benutzernamen** und das **Kennwort** für Ihren REST-API-Endpunkt an.
    :::image type="content" source="media/add-api-connector/api-connector-config.png" alt-text="Konfiguration der Standardauthentifizierung für einen API-Connector":::
6. Wählen Sie **Speichern** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-rest-api-username-and-password-policy-keys"></a>Hinzufügen von Richtlinienschlüsseln für REST-API-Benutzernamen und -Kennwörter

Um ein technisches REST-API-Profil mit HTTP-Standardauthentifizierung zu konfigurieren, müssen Sie die folgenden kryptografischen Schlüssel zum Speichern des Benutzernamens und des Kennworts erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **RestApiUsername** ein.
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den REST-API-Benutzernamen ein.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie erneut **Richtlinienschlüssel** aus.
1. Wählen Sie **Hinzufügen**.
1. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **RestApiPassword** ein.
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** das REST-API-Kennwort ein.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurieren Ihres technischen REST-API-Profils für die Verwendung der HTTP-Standardauthentifizierung

Konfigurieren Sie nach dem Erstellen der erforderlichen Schlüssel die Metadaten Ihres technischen REST-API-Profils so, dass sie auf die Anmeldeinformationen verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `Basic`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Nachfolgend finden Sie einen XML-Codeausschnitt als Beispiel für ein mit der HTTP-Standardauthentifizierung konfiguriertes technisches RESTful-Profi:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="https-client-certificate-authentication"></a>HTTPS-Clientzertifikatauthentifizierung

Bei der Clientzertifikatauthentifizierung handelt es sich um eine gegenseitige zertifikatbasierte Authentifizierung, bei der der Client (Azure AD B2C) sein Clientzertifikat für den Server bereitstellt, um seine Identität nachzuweisen. Dies erfolgt im Rahmen des SSL-Handshakes. Ihre API muss überprüfen, ob die Zertifikate zu einem gültigen Client (beispielsweise Azure AD B2C) gehören, und Autorisierungsentscheidungen treffen. Bei dem Clientzertifikat handelt es sich um ein digitales X.509-Zertifikat. 

> [!IMPORTANT]
> In Produktionsumgebungen muss das Zertifikat von einer Zertifizierungsstelle signiert werden.

### <a name="create-a-certificate"></a>Erstellen eines Zertifikats

#### <a name="option-1-use-azure-key-vault-recommended"></a>Option 1: Verwenden von Azure Key Vault (empfohlen)

Zum Erstellen eines Zertifikats können Sie den [Azure Key Vault](../key-vault/certificates/create-certificate.md) verwenden, der über Optionen für selbstsignierte Zertifikate und Integrationen mit Zertifikatausstelleranbietern für signierte Zertifikate verfügt. Empfohlene Einstellungen umfassen:
- **Betreff**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Inhaltstyp**: `PKCS #12`
- **Gültigkeitsdauer Aktionstyp**: `Email all contacts at a given percentage lifetime` oder `Email all contacts a given number of days before expiry`
- **Schlüsseltyp**: `RSA`
- **Schlüsselgröße**: `2048`
- **Exportierbarer privater Schlüssel**: `Yes` (um die Datei vom Typ `.pfx` exportieren zu können)

Sie können dann [das Zertifikat exportieren](../key-vault/certificates/how-to-export-certificate.md).

#### <a name="option-2-prepare-a-self-signed-certificate-using-powershell-module"></a>Option 2: Vorbereiten eines selbstsignierten Zertifikats per PowerShell-Modul

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

::: zone pivot="b2c-user-flow"

### <a name="configure-your-api-connector"></a>Konfigurieren Ihres API-Connectors

Führen Sie die folgenden Schritte aus, um einen API-Connector mit Authentifizierung über ein Clientzertifikat zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus.
3. Wählen Sie **API-Connectors** und dann den zu konfigurierenden **API-Connector** aus.
4. Wählen Sie als **Authentifizierungstyp** die Option **Zertifikat** aus.
5. Wählen Sie im Feld **Zertifikat hochladen** die PFX-Datei Ihres Zertifikats mit einem privaten Schlüssel aus.
6. Geben Sie im Feld **Kennwort eingeben** das Kennwort des Zertifikats ein.
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="Konfiguration der Zertifikatauthentifizierung für einen API-Connector":::
7. Wählen Sie **Speichern** aus.

### <a name="perform-authorization-decisions"></a>Treffen von Autorisierungsentscheidungen 
Ihre API muss die Autorisierung basierend auf gesendeten Client-Zertifikaten implementieren, um die API-Endpunkte zu schützen. Für den Azure App Service und die Azure Funktionen finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung](../app-service/app-service-web-configure-tls-mutual-auth.md) Informationen zum Aktivieren und *Validieren des Zertifikats über Ihren API-Code*.  Alternativ können Sie Azure API Management als Ebene vor einem beliebigen API-Dienst verwenden, um [Clientzertifikateigenschaften auf gewünschte Werte zu überprüfen](
../api-management/api-management-howto-mutual-certificates-for-clients.md).

### <a name="renewing-certificates"></a>Erneuern von Zertifikaten
Es wird empfohlen, Erinnerungswarnungen für den Zeitpunkt festzulegen, an dem das Zertifikat abläuft. Wenn die verwendeten Zertifikate ablaufen, müssen Sie ein neues Zertifikat generieren und die obigen Schritte wiederholen. Für den Übergang zu einem neuen Zertifikat kann Ihr API-Dienst während der Bereitstellung des neuen Zertifikats vorübergehend alte und neue Zertifikate akzeptieren. 

Um ein neues Zertifikat in einen bestehenden API-Anschluss hochzuladen, wählen Sie den API-Anschluss unter **API-Anschlüsse** aus und klicken Sie auf **Neues Zertifikat hochladen**. Von Azure AD B2C wird automatisch das zuletzt hochgeladene Zertifikat verwendet, das nicht abgelaufen ist und dessen Startdatum in der Vergangenheit liegt.

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="Bereitstellen eines neuen Zertifikats für einen API-Connector, wenn bereits ein Zertifikat vorhanden ist":::

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-a-client-certificate-policy-key"></a>Hinzufügen eines Richtlinienschlüssels für das Clientzertifikat

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie im Feld **Optionen** die Option **Upload** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **RestApiClientCertificate** ein.
    Das Präfix *B2C_1A_* wird automatisch hinzugefügt.
1. Wählen Sie im Feld **Dateiupload** die PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel aus.
1. Geben Sie im Feld **Kennwort** das Kennwort des Zertifikats ein.
1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurieren Ihres technischen REST-API-Profils für die Verwendung der Clientzertifikatauthentifizierung

Konfigurieren Sie nach dem Erstellen des erforderlichen Schlüssels die Metadaten Ihres technischen REST-API-Profils so, dass sie auf das Clientzertifikat verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `ClientCertificate`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Nachfolgend finden Sie einen XML-Codeausschnitt als Beispiel für ein mit einem HTTP-Clientzertifikat konfiguriertes technisches RESTful-Profi:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2-Bearerauthentifizierung 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Die Definition der Bearertokenauthentifizierung finden Sie unter [OAuth2.0 Authorization Framework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Bei der Bearertokenauthentifizierung sendet Azure AD B2C eine HTTP-Anforderung mit einem Token im Autorisierungsheader.

```http
Authorization: Bearer <token>
```

Ein Bearertoken ist eine nicht transparente Zeichenfolge. Dabei kann es sich um ein JWT-Zugriffstoken oder eine beliebige Zeichenfolge handeln, von dem/der die REST-API erwartet, dass es/sie von Azure AD B2C im Autorisierungsheader gesendet wird. Azure AD B2C unterstützt die folgenden Typen:

- **Bearertoken**. Damit das Bearertoken im technischen RESTful-Profil gesendet werden kann, muss Ihre Richtlinie das Bearertoken zuerst abrufen und dann im technischen RESTful-Profil verwenden.  
- **Statisches Bearertoken**. Verwenden Sie diesen Ansatz, wenn Ihre REST-API ein langfristiges Zugriffstoken ausgibt. Wenn Sie ein statisches Bearertoken verwenden möchten, müssen Sie einen Richtlinienschlüssel und dann einen Verweis vom technischen RESTful-Profil auf Ihren Richtlinienschlüssel erstellen. 


## <a name="using-oauth2-bearer"></a>Verwenden eines OAuth2-Bearertokens  

In den folgenden Schritten wird veranschaulicht, wie Sie mithilfe von Clientanmeldeinformationen ein Bearertoken abrufen und es im Autorisierungsheader der REST-API-Aufrufe übergeben.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definieren eines Anspruchs zum Speichern des Bearertokens

Ein Anspruch ermöglicht die temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie. Im [Anspruchsschema](claimsschema.md) deklarieren Sie Ihre Ansprüche. Das Zugriffstoken muss in einem Anspruch gespeichert werden, um später verwendet werden zu können. 

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die folgenden Ansprüche hinzu.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Abrufen eines Zugriffstokens 

Sie können ein Zugriffstoken auf eine von mehreren Arten abrufen: durch Abrufen des Tokens [von einem Verbundidentitätsanbieter](idp-pass-through-user-flow.md), durch Aufrufen einer REST-API, die ein Zugriffstoken zurückgibt, mithilfe eines [ROPC-Flows](../active-directory/develop/v2-oauth-ropc.md) oder mithilfe des [Clientanmeldeinformationsflows](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Der Clientanmeldeinformationsflow wird häufig für Interaktionen zwischen Servern verwendet, die ohne Benutzereingriff im Hintergrund ausgeführt werden müssen.

#### <a name="acquiring-an-azure-ad-access-token"></a>Abrufen eines Azure AD-Zugriffstokens 

Im folgenden Beispiel wird ein technisches REST-API-Profil verwendet, um eine Anforderung an den Azure AD-Tokenendpunkt zu senden, wobei die Clientanmeldeinformationen als HTTP-Standardauthentifizierung verwendet werden. Weitere Informationen finden Sie unter [Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). 

Bevor das technische Profil mit Azure AD interagieren kann, um ein Zugriffstoken abzurufen, müssen Sie eine Anwendung registrieren. Azure AD B2C basiert auf der Azure AD-Plattform. Sie können die App in Ihrem Azure AD B2C-Mandanten oder in einem beliebigen Azure AD-Mandanten erstellen, den Sie verwalten. So registrieren Sie eine Anwendung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen | Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie im linken Menü **Azure Active Directory** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispiel: *Client_Credentials_Auth_app*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** .

Für einen Clientanmeldeinformationenflow müssen Sie ein Anwendungsgeheimnis erstellen. Der geheime Clientschlüssel wird auch als Anwendungskennwort bezeichnet. Das Geheimnis wird von Ihrer Anwendung verwendet, um ein Zugriffstoken zu erhalten.

1. Wählen Sie auf der Seite **Azure AD – App-Registrierungen** die erstellte Anwendung (z. B. *Client_Credentials_Auth_app*) aus.
1. Wählen Sie im linken Menü unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie **Neuer geheimer Clientschlüssel**.
1. Geben Sie im Feld **Beschreibung** eine Beschreibung für das Clientgeheimnis ein. Beispielsweise *clientsecret1*.
1. Wählen Sie unter **Läuft ab** einen Zeitraum aus, für den das Geheimnis gültig ist, und wählen Sie dann **Hinzufügen** aus.
1. Notieren Sie sich den **Wert** des Geheimnisses, das in Ihrem Clientanwendungscode verwendet werden soll. Dieser Geheimniswert kann nach Verlassen dieser Seite nicht erneut angezeigt werden. Sie verwenden diesen Wert als Anwendungsgeheimnis im Code Ihrer Anwendung.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Erstellen der Azure AD B2C-Richtlinienschlüssel

Sie müssen die Client-ID und den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein, `SecureRESTClientId`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** die Client-ID ein, die Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.
1. Erstellen Sie einen weiteren Richtlinienschlüssel mit den folgenden Einstellungen:
    - **Name**: `SecureRESTClientSecret`.
    - **Geheimnis**: Geben Sie den geheimen Clientschlüssel ein, den Sie zuvor notiert haben

Ersetzen Sie im „ServiceUrl“-Element den Platzhalter „your-tenant-name“ durch den Namen Ihres Azure AD-Mandanten. Alle verfügbaren Optionen finden Sie unter [Definieren eines technischen RESTful-Profils](restful-technical-profile.md).

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

> [!NOTE]
> Wenn Sie die Ansprüche `grant_type` oder `scope` in anderen technischen Profilen verwenden, wird empfohlen, dass sie auch `DefaultValue` angeben und `AlwaysUseDefaultValue="true"` verwenden, um potenzielle Konflikte bei der Bindung mit dem falschen Wert zu vermeiden.

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Ändern des technischen REST-Profils zur Verwendung der Bearertokenauthentifizierung

Um die Bearertokenauthentifizierung in Ihrer benutzerdefinierten Richtlinie zu unterstützen, müssen Sie das technische REST-API-Profil wie folgt ändern:

1. Öffnen Sie die Erweiterungsrichtliniendatei *TrustFrameworkExtensions.xml* in Ihrem Arbeitsverzeichnis.
1. Suchen Sie den `<TechnicalProfile>`-Knoten, der `Id="REST-API-SignUp"` enthält.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) wie folgt in *Bearer*:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Ändern Sie das Element *UseClaimAsBearerToken* in *bearerToken*, oder fügen Sie es wie folgt hinzu. *bearerToken* ist der Name des Anspruchs, von dem das Bearertoken abgerufen wird (der Ausgabeanspruch von `REST-AcquireAccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Stellen Sie sicher, dass Sie den oben verwendeten Anspruch als Eingabeanspruch hinzufügen:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Nachdem Sie den obigen Codeausschnitt hinzugefügt haben, sollte Ihr technisches Profil wie der folgende XML-Code aussehen:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Verwenden eines statischen OAuth2-Bearertokens 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Hinzufügen des Richtlinienschlüssels für das OAuth2-Bearertoken

Rufen Sie zum Konfigurieren eines technischen REST-API-Profils mit einem OAuth2-Bearertoken ein Zugriffstoken vom REST-API-Besitzer ab. Erstellen Sie dann den folgenden kryptografischen Schlüssel zum Speichern des Bearertokens.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `RestApiBearerToken`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Encryption` aus.
1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurieren Ihres technischen REST-API-Profils für die Verwendung des Bearertoken-Richtlinienschlüssels

Konfigurieren Sie nach dem Erstellen des erforderlichen Schlüssels die Metadaten Ihres technischen REST-API-Profils so, dass sie auf das Bearertoken verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `Bearer`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Nachfolgend finden Sie einen XML-Codeausschnitt als Beispiel für ein mit der Bearertokenauthentifizierung konfiguriertes technisches RESTful-Profi:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end


## <a name="api-key-authentication"></a>Authentifizierung mit API-Schlüssel

::: zone pivot="b2c-user-flow"

Von einigen Diensten wird ein API-Schlüsselmechanismus verwendet, um den Zugriff auf Ihre HTTP-Endpunkte während der Entwicklung zu verschleiern. In diesem Fall muss der Aufrufer einen eindeutigen Schlüssel als HTTP-Header oder HTTP-Abfrageparameter einschließen. Für [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) können Sie dies erreichen, indem Sie `code` als Abfrageparameter in die **Endpunkt-URL** Ihres API-Connectors einschließen. Beispiel: `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>. 

Hierbei handelt es sich nicht um einen Mechanismus, der allein in der Produktion verwendet werden sollte. Daher ist die Konfiguration für die Standard- oder Zertifikatauthentifizierung immer erforderlich. Wenn Sie für Entwicklungszwecke keine Authentifizierungsmethode implementieren möchten (nicht empfohlen), können Sie in der API-Connectorkonfiguration die Standardauthentifizierung auswählen und temporäre Werte für `username` und `password` verwenden, die von der API ignoriert werden können, während Sie die ordnungsgemäße Autorisierung implementieren.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ein API-Schlüssel ist ein eindeutiger Bezeichner, der zum Authentifizieren eines Benutzers für den Zugriff auf einen Rest-API-Endpunkt verwendet wird. Der Schlüssel wird in einem benutzerdefinierten HTTP-Header gesendet. Der [HTTP-Trigger in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) beispielsweise verwendet `x-functions-key` im HTTP-Header, um den Anforderer zu identifizieren.  

### <a name="add-api-key-policy-keys"></a>Hinzufügen von Richtlinienschlüsseln als API-Schlüssel

Um ein technisches REST-API-Profil für die Authentifizierung mit API-Schlüssel zu konfigurieren, müssen Sie den folgenden kryptografischen Schlüssel zum Speichern des API-Schlüssels erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.
1. Geben Sie **RestApiKey** als **Name** ein.
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den REST-API-Schlüssel ein.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Konfigurieren des technischen REST-API-Profils für die Authentifizierung mit API-Schlüssel

Konfigurieren Sie nach dem Erstellen des erforderlichen Schlüssels die Metadaten Ihres technischen REST-API-Profils so, dass sie auf die Anmeldeinformationen verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `ApiKeyHeader`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

Die **ID** des kryptografischen Schlüssels definiert den HTTP-Header. In diesem Beispiel wird der API-Schlüssel als **x-functions-key** gesendet.

Nachfolgend finden Sie einen XML-Codeausschnitt als Beispiel für ein technisches RESTful-Profil, das für die Authentifizierung mit API-Schlüssel zum Aufrufen einer Azure-Funktion konfiguriert wurde:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="b2c-user-flow"
- Erste Schritte mit den [Beispielen](api-connector-samples.md#api-connector-rest-api-samples)
::: zone-end

::: zone pivot="b2c-custom-policy"
- Weitere Informationen zum Element für das [technische RESTful-Profil](restful-technical-profile.md) finden Sie in der Referenz zu benutzerdefinierten Richtlinien.
::: zone-end
