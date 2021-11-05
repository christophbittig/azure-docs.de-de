---
title: Fehler und Ausnahmen (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Fehler und Ausnahmen, den bedingten Zugriff und Anspruchsabfragen in MSAL-Android-Anwendungen verarbeiten.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: marsma
ms.reviewer: marsma
ms.openlocfilehash: 3fa935264cadf78c2fcea1d421806a7b983944a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036147"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Behandeln von Ausnahmen und Fehlern in der MSAL für Android

Ausnahmen in der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) sollen App-Entwicklern bei der Problembehandlung ihrer Anwendung helfen. Ausnahmemeldungen sind nicht lokalisiert.

Bei der Verarbeitung von Ausnahmen und Fehlern können Sie den Ausnahmetyp selbst und den Fehlercode verwenden, um zwischen Ausnahmen zu unterscheiden.  Eine Liste der Fehlercodes finden Sie unter [Authentifizierungs- und Autorisierungsfehlercodes](reference-aadsts-error-codes.md).

Während der Anmeldung treten möglicherweise Fehler bei Zustimmungen, bedingtem Zugriff (MFA, Geräteverwaltung, standortbezogene Einschränkungen), Tokenausstellung und -einlösung sowie Benutzereigenschaften auf.


|Fehlerklasse | Ursache/Fehlerzeichenfolge| Fehlerbehandlung |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: The refresh token used to redeem access token is invalid, expired, or revoked. This exception may be because of a password change. </li><li>`NO_TOKENS_FOUND`: Access token doesn't exist and no refresh token can be found to redeem access token.</li> <li>Step-up required<ul><li>MFA</li><li>Missing claims</li></ul></li><li>Blocked by Conditional Access (for example, [authentication broker](./msal-android-single-sign-on.md) installation required)</li><li>`NO_ACCOUNT_FOUND`: No account available in the cache for silent authentication.</li></ul> |Rufen Sie `acquireToken()` auf, um den Benutzer aufzufordern, Benutzernamen und Kennwort einzugeben und ggf. eine mehrstufige Authentifizierung durchzuführen.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: User or server has not accepted all scopes. The server may decline a scope if the requested scope is not supported, not recognized, or not supported for a particular account. </li></ul>| Der Entwickler muss entscheiden, ob die Authentifizierung mit den gewährten Bereichen fortgesetzt oder der Authentifizierungsprozess beendet wird. Es gibt die Option, die Anforderung zum Abrufen eines Tokens nur für die gewährten Bereiche erneut zu übermitteln und Hinweise bereitzustellen, welche Berechtigungen gewährt wurden. Hierfür wird `silentParametersForGrantedScopes` übergeben und `acquireTokenSilent` aufgerufen. |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: This request is missing a required parameter, includes an invalid parameter, includes a parameter more than once, or is otherwise malformed. </li><li>`SERVICE_NOT_AVAILABLE`: Represents 500/503/506 error codes due to the service being down. </li><li>`UNAUTHORIZED_REQUEST`: The client is not authorized to request an authorization code.</li><li>`ACCESS_DENIED`: Der Ressourcenbesitzer oder Autorisierungsserver hat die Anforderung verweigert.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` validation failed</li><li>`UNKNOWN_ERROR`: Request to server failed, but no error and `error_description` are returned back from the service.</li><ul>| Diese Ausnahmeklasse repräsentiert Fehler bei der Kommunikation mit dem Dienst und kann aus den Autorisierungs- oder Tokenendpunkten stammen. Die MSAL liest den Fehler und die Fehlerbeschreibung aus der Serverantwort. Im Allgemeinen werden diese Fehler behoben, indem die App-Konfigurationen entweder im Code oder im App-Registrierungsportal korrigiert werden. In seltenen Fällen kann diese Warnung durch einen Dienstausfall ausgelöst werden. Dieses Problem lässt sich nicht lösen, es muss auf die Wiederherstellung des Diensts gewartet werden.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: There are multiple cache entries found and the sdk cannot identify the correct access or refresh token from the cache. This exception usually indicates a bug in the sdk for storing tokens or that the authority is not provided in the silent request and multiple matching tokens are found. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: No active network is available on the device. </li><li>`JSON_PARSE_FAILURE`: The sdk failed to parse the JSON format.</li><li>`IO_ERROR`: `IOException` happened, could be a device or network error. </li><li>`MALFORMED_URL`: The URL is malformed. Likely caused when constructing the auth request, authority, or redirect URI. </li><li>`UNSUPPORTED_ENCODING`: The encoding is not supported by the device. </li><li>`NO_SUCH_ALGORITHM`: The algorithm used to generate [PKCE](https://tools.ietf.org/html/rfc7636) challenge is not supported. </li><li>`INVALID_JWT`: `JWT` returned by the server is not valid or is empty or malformed. </li><li>`STATE_MISMATCH`: State from authorization response did not match the state in the authorization request. For authorization requests, the sdk will verify the state returned from redirect and the one sent in the request. </li><li>`UNSUPPORTED_URL`: Unsupported URL, cannot perform ADFS authority validation. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: The authority is not supported for authority validation. The sdk supports B2C authorities, but doesn't support B2C authority validation. Only well-known host will be supported. </li><li>`CHROME_NOT_INSTALLED`: Chrome is not installed on the device. The sdk uses chrome custom tab for authorization requests if available, and will fall back to chrome browser. </li><li>`USER_MISMATCH`: The user provided in the acquire token request doesn't match the user returned from server.</li></ul>|Diese Ausnahmeklasse repräsentiert allgemeine Fehler, die lokal in der Bibliothek auftreten. Diese Ausnahmen können durch Korrigieren der Anforderung behandelt werden.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: The user initiated interactive flow and prior to receiving tokens back they canceled the request. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: Authority must be specified for `acquireTokenSilent`.</li></ul>|Diese Fehler können behoben werden, indem der Entwickler Argumente korrigiert und Aktivitäten für die interaktive Authentifizierung, für Rückrufe zum Abschluss und für Bereiche sicherstellt. Außerdem muss ein Konto mit einer gültigen ID bereitgestellt worden sein.|


## <a name="catching-errors"></a>Abfangen von Fehlern

Der folgende Codeausschnitt zeigt ein Beispiel für das Abfangen von Fehlern im Fall von automatischen `acquireToken`-Aufrufen.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Protokollierung in MSAL für Android finden Sie [hier](msal-logging-android.md).
