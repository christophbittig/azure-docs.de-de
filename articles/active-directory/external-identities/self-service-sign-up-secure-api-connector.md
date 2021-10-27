---
title: Schützen von APIs, die als API-Connectors in Benutzerflows für die Self-Service-Registrierung von Azure AD verwendet werden
description: Hier erfahren Sie, wie Sie Ihre benutzerdefinierten RESTful-APIs schützen, die als API-Connectors in Benutzerflows für die Self-Service-Registrierung verwendet werden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/16/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9086e3df2022822e890af4d187f35a55bad5ae03
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074112"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-external-identities-self-service-sign-up-user-flows"></a>Schützen Ihrer API, die als API-Connector in Benutzerflows für die Self-Service-Registrierung von Azure AD External Identities verwendet wird

Wenn Sie eine REST-API in einen Benutzerflow für die Self-Service-Registrierung von Azure AD External Identities integrieren, müssen Sie Ihren REST-API-Endpunkt mittels Authentifizierung schützen. Durch diese REST-API-Authentifizierung wird sichergestellt, dass nur Dienste mit ordnungsgemäßen Anmeldeinformationen (beispielsweise Azure AD) Aufrufe an Ihren Endpunkt senden können. In diesem Artikel wird erläutert, wie die REST-API geschützt wird. 

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die Schritte im Leitfaden [Exemplarische Vorgehensweise: Hinzufügen eines API-Connectors zu einem Benutzerflow für die Registrierung](self-service-sign-up-add-api-connector.md) aus.

Sie können Ihren API-Endpunkt entweder mithilfe der HTTP-Standardauthentifizierung oder mithilfe der HTTPS-Clientzertifikatauthentifizierung schützen. In beiden Fällen müssen Sie die Anmeldeinformationen angeben, die von Azure AD beim Aufrufen Ihres API-Endpunkts verwendet werden sollen. Der API-Endpunkt überprüft dann die Anmeldeinformationen und führt Autorisierungsentscheidungen aus.


## <a name="http-basic-authentication"></a>HTTP-Standardauthentifizierung

Die HTTP-Standardauthentifizierung ist in [RFC 2617](https://tools.ietf.org/html/rfc2617) definiert. Die Standardauthentifizierung funktioniert wie folgt: Azure AD sendet eine HTTP-Anforderung mit den Clientanmeldeinformationen (`username` und `password`) im Header `Authorization`. Die Anmeldeinformationen werden als Base64-codierte Zeichenfolge im Format `username:password` angegeben. Ihre API ist dann für die Überprüfung dieser Werte verantwortlich, um andere Autorisierungsentscheidungen zu treffen.

Führen Sie die folgenden Schritte aus, um einen API-Connector mit HTTP-Standardauthentifizierung zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD** aus.
3. Wählen Sie **API-Connectors** und dann den zu konfigurierenden **API-Connector** aus.
4. Wählen Sie als **Authentifizierungstyp** die Option **Standard** aus.
5. Geben Sie den **Benutzernamen** und das **Kennwort** für Ihren REST-API-Endpunkt an.
    :::image type="content" source="media/secure-api-connector/api-connector-config.png" alt-text="Konfiguration der Standardauthentifizierung für einen API-Connector":::
6. Wählen Sie **Speichern** aus.

## <a name="https-client-certificate-authentication"></a>HTTPS-Clientzertifikatauthentifizierung

Bei der Clientzertifikatauthentifizierung handelt es sich um eine gegenseitige zertifikatbasierte Authentifizierung, bei der der Client (Azure AD) sein Clientzertifikat für den Server bereitstellt, um seine Identität nachzuweisen. Dies erfolgt im Rahmen des SSL-Handshakes. Ihre API muss überprüfen, ob die Zertifikate zu einem gültigen Client (beispielsweise Azure AD) gehören, und Autorisierungsentscheidungen treffen. Bei dem Clientzertifikat handelt es sich um ein digitales X.509-Zertifikat. 

> [!IMPORTANT]
> In Produktionsumgebungen muss das Zertifikat von einer Zertifizierungsstelle signiert werden.

### <a name="create-a-certificate"></a>Erstellen eines Zertifikats

#### <a name="option-1-use-azure-key-vault-recommended"></a>Option 1: Verwenden von Azure Key Vault (empfohlen)

Zum Erstellen eines Zertifikats können Sie den [Azure Key Vault](../../key-vault/certificates/create-certificate.md) verwenden, der über Optionen für selbstsignierte Zertifikate und Integrationen mit Zertifikatausstelleranbietern für signierte Zertifikate verfügt. Empfohlene Einstellungen umfassen:
- **Betreff**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Inhaltstyp**: `PKCS #12`
- **Gültigkeitsdauer Aktionstyp**: `Email all contacts at a given percentage lifetime` oder `Email all contacts a given number of days before expiry`
- **Schlüsseltyp**: `RSA`
- **Schlüsselgröße**: `2048`
- **Exportierbarer privater Schlüssel**: `Yes` (um die Datei vom Typ `.pfx` exportieren zu können)

Sie können dann [das Zertifikat exportieren](../../key-vault/certificates/how-to-export-certificate.md).

#### <a name="option-2-prepare-a-self-signed-certificate-using-powershell"></a>Option 2: Erstellen eines selbstsignierten Zertifikats mit PowerShell

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="configure-your-api-connector"></a>Konfigurieren Ihres API-Connectors

Führen Sie die folgenden Schritte aus, um einen API-Connector mit Authentifizierung über ein Clientzertifikat zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure AD** aus.
3. Wählen Sie **API-Connectors** und dann den zu konfigurierenden **API-Connector** aus.
4. Wählen Sie als **Authentifizierungstyp** die Option **Zertifikat** aus.
5. Wählen Sie im Feld **Zertifikat hochladen** die PFX-Datei Ihres Zertifikats mit einem privaten Schlüssel aus.
6. Geben Sie im Feld **Kennwort eingeben** das Kennwort des Zertifikats ein.
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="Konfiguration der Zertifikatauthentifizierung für einen API-Connector":::
7. Wählen Sie **Speichern** aus.

### <a name="perform-authorization-decisions"></a>Treffen von Autorisierungsentscheidungen 
Ihre API muss die Autorisierung basierend auf gesendeten Client-Zertifikaten implementieren, um die API-Endpunkte zu schützen. Für den Azure App Service und die Azure Funktionen finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung](../../app-service/app-service-web-configure-tls-mutual-auth.md) Informationen zum Aktivieren und *Validieren des Zertifikats über Ihren API-Code*.  Alternativ können Sie Azure API Management als Ebene vor einem beliebigen API-Dienst verwenden, um [Clientzertifikateigenschaften auf gewünschte Werte zu überprüfen](
../../api-management/api-management-howto-mutual-certificates-for-clients.md).

### <a name="renewing-certificates"></a>Erneuern von Zertifikaten
Es wird empfohlen, Erinnerungswarnungen für den Zeitpunkt festzulegen, an dem das Zertifikat abläuft. Wenn die verwendeten Zertifikate ablaufen, müssen Sie ein neues Zertifikat generieren und die obigen Schritte wiederholen. Für den Übergang zu einem neuen Zertifikat kann Ihr API-Dienst während der Bereitstellung des neuen Zertifikats vorübergehend alte und neue Zertifikate akzeptieren. 

Um ein neues Zertifikat in einen bestehenden API-Anschluss hochzuladen, wählen Sie den API-Anschluss unter **API-Anschlüsse** aus und klicken Sie auf **Neues Zertifikat hochladen**. Von Azure AD wird automatisch das zuletzt hochgeladene Zertifikat verwendet, das nicht abgelaufen ist und dessen Startdatum in der Vergangenheit liegt.

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="Bereitstellen eines neuen Zertifikats für einen API-Connector, wenn bereits ein Zertifikat vorhanden ist":::

## <a name="api-key-authentication"></a>Authentifizierung mit API-Schlüssel

Von einigen Diensten wird ein API-Schlüsselmechanismus verwendet, um den Zugriff auf Ihre HTTP-Endpunkte während der Entwicklung zu verschleiern. In diesem Fall muss der Aufrufer einen eindeutigen Schlüssel als HTTP-Header oder HTTP-Abfrageparameter einschließen. Für [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) können Sie dies erreichen, indem Sie `code` als Abfrageparameter in die **Endpunkt-URL** Ihres API-Connectors einschließen. Beispiel: `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>. 

Hierbei handelt es sich nicht um einen Mechanismus, der allein in der Produktion verwendet werden sollte. Daher ist die Konfiguration für die Standard- oder Zertifikatauthentifizierung immer erforderlich. Wenn Sie für Entwicklungszwecke keine Authentifizierungsmethode implementieren möchten (nicht empfohlen), können Sie in der API-Connectorkonfiguration die Standardauthentifizierung auswählen und temporäre Werte für `username` und `password` verwenden, die von der API ignoriert werden können, während Sie die ordnungsgemäße Autorisierung implementieren.

## <a name="next-steps"></a>Nächste Schritte
- Verwenden Sie unsere [Schnellstartbeispiele](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), um direkt loszulegen.
