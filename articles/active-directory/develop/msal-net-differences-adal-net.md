---
title: Unterschiede zwischen ADAL.NET- und MSAL.NET-Apps | Azure
titleSuffix: Microsoft identity platform
description: Hier werden die Unterschiede zwischen der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) und der Azure AD-Authentifizierungsbibliothek für .NET (ADAL.NET) erläutert.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 06/09/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5dfc0c3c006c42f6e56e9f2f15311a2b99396fe4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424095"
---
# <a name="differences-between-adalnet-and-msalnet-apps"></a>Unterschiede zwischen ADAL.NET- und MSAL.NET-Apps

Die Migration Ihrer Anwendungen von ADAL zu MSAL trägt zur Verbesserung der Sicherheit und Resilienz bei. In diesem Artikel werden Unterschiede zwischen ADAL.NET und MSAL.NET beschrieben. In den meisten Fällen sollten Sie MSAL.NET und Microsoft Identity Platform verwenden, da es sich hierbei um die neueste Generation von Microsoft-Authentifizierungsbibliotheken handelt. Mithilfe von MSAL.NET rufen Sie Token für Benutzer ab, die sich mit Azure AD (Geschäfts-, Schul- und Unikonten), (persönlichen) Microsoft-Konten (MSA) oder Azure AD B2C bei Ihrer Anwendung anmelden.

Wenn Sie bereits mit ADAL.NET und dem Endpunkt von Azure AD für Entwickler (v1.0) vertraut sind, lesen Sie den Artikel [Gründe für eine Aktualisierung auf die Microsoft Identity Platform (v2.0)](../azuread-dev/azure-ad-endpoint-comparison.md). Wenn Benutzer in Ihrer Anwendung mit früheren Versionen von [Active Directory-Verbunddienste (AD FS)](/windows-server/identity/active-directory-federation-services) angemeldet werden müssen, benötigen Sie weiterhin ADAL.NET. Weitere Informationen finden Sie unter [ADFS-Support](https://aka.ms/msal-net-adfs-support).

|   | **ADAL.NET**  | **MSAL.NET**  |
|-----------------------------|--------------------------------------------|---------------------|
| **NuGet-Pakete und Namespaces**  |ADAL.NET wurde vom NuGet-Paket [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) genutzt. Der Namespace lautete `Microsoft.IdentityModel.Clients.ActiveDirectory`.  | Fügen Sie das NuGet-Paket [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) hinzu, und verwenden Sie den Namespace `Microsoft.Identity.Client`. Wenn Sie eine vertrauliche Clientanwendung erstellen, sehen Sie sich [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) an. |
| **Bereiche und Ressourcen**  | Von ADAL.NET werden Token für *Ressourcen* abgerufen. |  Von MSAL.NET werden Token für *Bereiche* abgerufen. Für einige `AcquireTokenXXX`-Überschreibungen von MSAL.NET ist ein Parameter namens „scopes(`IEnumerable<string> scopes`)“ erforderlich. Bei diesem Parameter handelt es sich um eine einfache Liste mit Zeichenfolgen, die die angeforderten Berechtigungen und Ressourcen deklarieren. Bekannte Geltungsbereiche sind beispielsweise die [Geltungsbereiche von Microsoft Graph](/graph/permissions-reference). Sie können auch [mit MSAL.NET auf v1.0-Ressourcen zugreifen](#scopes). |
| **Core-Klassen**  | Von ADAL.NET wurde [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) verwendet, um Ihre Verbindung mit dem Sicherheitstokendienst (Security Token Service, STS) oder Autorisierungsserver über eine Autorität darzustellen. | MSAL.NET ist für [Clientanwendungen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) konzipiert. Es definiert Schnittstellen vom Typ `IPublicClientApplication` für öffentliche Clientanwendungen und `IConfidentialClientApplication` für vertrauliche Client-Anwendungen sowie eine Basisschnittstelle (`IClientApplicationBase`) für den Vertrag, der für beide Arten von Anwendungen üblich ist.|
| **Tokenabruf**  | In öffentlichen Clients verwendet ADAL `AcquireTokenAsync` und `AcquireTokenSilentAsync` für Authentifizierungsaufrufe. | In öffentlichen Clients verwendet MSAL `AcquireTokenInteractive` und `AcquireTokenSilent` für die gleichen Authentifizierungsaufrufe. Die Parameter unterscheiden sich von den ADAL-Parametern. <br><br>In vertrauenswürdigen Clientanwendungen gibt es je nach Szenario [Tokenabrufmethoden](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-Tokens) mit einem expliziten Namen. Ein weiterer Unterschied besteht darin, dass Sie in MSAL.NET nicht mehr in jedem AcquireTokenXX-Aufruf die Client-ID (`ClientID`) Ihrer Anwendung übergeben müssen. Die Client-ID (`ClientID`) wird lediglich einmal beim Erstellen von `IPublicClientApplication` oder `IConfidentialClientApplication` festgelegt.|
|  **„IAccount“ und „IUser“**  | ADAL definiert das Konzept „Benutzer“ (über die Schnittstelle „IUser“). Ein Benutzer ist jedoch entweder eine Person oder ein Software-Agent. Somit kann ein Benutzer einzelne oder mehrere Konten in Microsoft Identity Platform besitzen (mehrere Azure AD-Konten, Azure AD B2C, persönliche Microsoft-Konten). Der Benutzer kann auch für einzelne oder mehrere Microsoft Identity Platform-Konten zuständig sein. | MSAL.NET definiert das Konzept „Konto“ (über die Schnittstelle „IAccount“). Die Schnittstelle „IAccount“ stellt Informationen zu einem einzelnen Konto dar. Der Benutzer kann über mehrere Konten in unterschiedlichen Mandanten verfügen. MSAL.NET bietet bessere Informationen in Gastszenarien, da Informationen zum Stammkonto bereitgestellt werden. Weitere Informationen zu den Unterschieden zwischen „IUser“ und „IAccount“ finden Sie [hier](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/msal-net-2-released#iuser-is-replaced-by-iaccount).|
|  **Cachepersistenz**  | ADAL.NET unterstützt die Erweiterung der `TokenCache`-Klasse. Auf diese Weise können die gewünschten Persistenzfunktionen auf Plattformen ohne sicheren Speicher (.NET Framework und .NET Core) mithilfe der `BeforeAccess`-Methode und `BeforeWrite`-Methode implementiert werden. Ausführliche Informationen finden Sie im [Artikel zur Tokencacheserialisierung in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization). | In MSAL.NET stellt der Tokencache eine versiegelte Klasse dar und kann daher nicht erweitert werden. Somit muss die Tokencachepersistenz in Form einer Hilfsklasse implementiert werden, die mit dem versiegelten Tokencache interagiert. Diese Interaktion wird im [Artikel zur Tokencacheserialisierung in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) beschrieben. Die Serialisierung für eine öffentliche Clientanwendung (siehe [Tokencache für eine öffentliche Clientanwendung](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)) unterscheidet sich von der Serialisierung für eine vertrauliche Clientanwendung (siehe [Tokencache für eine Web-App oder Web-API](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)). |
| **common-Autorität** | ADAL verwendet Azure AD v1.0. Die Autorität `https://login.microsoftonline.com/common` in Azure AD v1.0 (von ADAL verwendet) ermöglicht Benutzern die Anmeldung mit einem beliebigen AAD-Organisationskonto (Geschäfts-, Schul-oder Unikonto). Anmeldungen mit persönlichen Microsoft-Konten sind bei Azure AD v1.0 nicht möglich. Weitere Informationen finden Sie im [Artikel zur Autoritätsüberprüfung in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation). | MSAL verwendet Azure AD v2.0. Die Autorität `https://login.microsoftonline.com/common` in Azure AD v2.0 (von MSAL verwendet) ermöglicht Benutzern die Anmeldung mit einem beliebigen AAD-Organisationskonto (Geschäfts-, Schul-oder Unikonto) oder mit einem persönlichen Microsoft-Konto. Wenn Sie in MSAL nur Anmeldungen mit Organisationskonten (Geschäfts-, Schul- oder Unikonto) zulassen möchten, müssen Sie den Endpunkt `https://login.microsoftonline.com/organizations` verwenden.  Ausführliche Informationen finden Sie unter dem `authority`-Parameter in [öffentlichen Clientanwendungen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication). |

## <a name="supported-grants"></a>Unterstützte Gewährungstypen

In der folgenden Zusammenfassung werden die von ADAL.NET und MSAL.NET unterstützten Zuweisungen für öffentliche und vertrauliche Clientanwendungen miteinander verglichen:

### <a name="public-client-applications"></a>Öffentliche Clientanwendungen

In der folgenden Abbildung sind einige der Unterschiede zwischen ADAL.NET und MSAL.NET für eine öffentliche Clientanwendung zusammengefasst:

[![Screenshot: Einige der Unterschiede zwischen ADAL.NET und MSAL.NET für eine öffentliche Clientanwendung](media/msal-compare-msaldotnet-and-adaldotnet/differences.png)](media/msal-compare-msaldotnet-and-adaldotnet/differences.png#lightbox)

Für desktopbasierte und mobile Anwendungen werden in ADAL.NET und MSAL.NET folgende Zuweisungen unterstützt:

Erteilen                             | MSAL.NET                                                                                                                     | ADAL.NET                                                                                                                                                                                                   |
--------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Interactive                       | [Interaktiver Abruf von Token in MSAL.NET](scenario-desktop-acquire-token-interactive.md)    | [Interaktive Authentifizierung](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows)                                              |
Integrierte Windows-Authentifizierung | [Integrierte Windows-Authentifizierung](scenario-desktop-acquire-token-integrated-windows-authentication.md)         | [Integrierte Authentifizierung in Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos))  |
Benutzername und Kennwort               | [Authentifizierung mit Benutzername und Kennwort](scenario-desktop-acquire-token-username-password.md)                      | [Abruf von Token mit Benutzername und Kennwort](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)                                        |
Gerätecodefluss                  | [Gerätecodeflow](scenario-desktop-acquire-token-device-code-flow.md)                    | [Geräteprofil für Geräte ohne Webbrowser](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers)                                |

### <a name="confidential-client-applications"></a>Vertrauliche Clientanwendungen

In der folgenden Abbildung sind einige der Unterschiede zwischen ADAL.NET und MSAL.NET für eine vertrauliche Clientanwendung zusammengefasst:
 
[![Screenshot: Einige der Unterschiede zwischen ADAL.NET und MSAL.NET für eine vertrauliche Clientanwendung](media/msal-net-migration/confidential-client-application.png)](media/msal-net-migration/confidential-client-application.png#lightbox)


Für Webanwendungen, Web-APIs und Daemonanwendungen werden in ADAL.NET, MSAL.NET und „Microsoft.Identity.Web“ folgende Zuweisungen unterstützt:

Anwendungstyp              | Erteilen              | MSAL.NET                                                                                                                             | ADAL.NET                                                                                                                                                                                              |
------------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Web-App, Web-API, Daemon | Clientanmeldeinformationen | [Flow von Clientanmeldeinformationen in MSAL.NET](scenario-daemon-acquire-token.md#acquiretokenforclient-api)                        | [Flow von Clientanmeldeinformationen in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows)                                                               |
Web-API                  | OBO (Im Namen von)       | [OBO in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)                 | [Dienst-zu-Dienst-Aufrufe im Namen des Benutzers mit ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user)               |
Web-App                  | Authentifizierungscode          | [Abrufen von Token mit Autorisierungscodes für Web-Apps mit MSAL.NET](scenario-web-app-call-api-acquire-token.md)  | [Abrufen von Token mit Autorisierungscodes für Web-Apps mit ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) |

## <a name="migrating-from-adal-2x-with-refresh-tokens"></a>Migrieren von ADAL 2.x mit Aktualisierungstoken

In ADAL.NET v2.X wurden die Aktualisierungstoken offengelegt, sodass Sie Lösungen rund um den Tokeneinsatz entwickeln konnten. Dazu wurden die Token zwischengespeichert und die von ADAL 2.x bereitgestellten `AcquireTokenByRefreshToken`-Methoden verwendet.

Einige Lösungen wurden beispielsweise in folgenden Szenarien verwendet:

- Zeitintensive Dienste, die Aktionen wie etwa das Aktualisieren von Dashboards für Benutzer durchführen, wenn die Benutzer nicht mehr mit der App verbunden bzw. nicht mehr bei der App angemeldet sind.
- WebFarm-Szenarien, die es dem Client ermöglichen, das Aktualisierungstoken im Webdienst zur Verfügung zu stellen. (Die Zwischenspeicherung erfolgt nicht auf der Serverseite, sondern auf der Clientseite in einem verschlüsselten Cookie.)

Von MSAL.NET werden aus Sicherheitsgründen keine Aktualisierungstoken verfügbar gemacht. MSAL übernimmt das Aktualisieren von Token für Sie.

Praktischerweise verfügt MSAL.NET über eine API, die die Migration früherer (mit ADAL abgerufener) Aktualisierungstoken zu `IConfidentialClientApplication` unterstützt:

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

Dank dieser Methode können Sie das zuvor verwendete Aktualisierungstoken zusammen mit beliebigen Bereichen (Ressourcen) bereitstellen. Das Aktualisierungstoken wird durch ein neues ersetzt und in Ihrer Anwendung zwischengespeichert.

Da diese Methode für untypische Szenarien vorgesehen ist, ist sie nicht ohne Weiteres über `IConfidentialClientApplication` zugänglich. Vielmehr ist zunächst eine Umwandlung in `IByRefreshToken` erforderlich.

Der folgende Codeausschnitt enthält Migrationscode in einer vertraulichen Clientanwendung.

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

`GetCachedRefreshTokenForSignedInUser` ruft das Aktualisierungstoken ab, das von einer früheren Version der Anwendung, die zuvor ADAL 2.x verwendet hat, in einem Speicher abgelegt wurde. `GetTokenCacheForSignedInUser` deserialisiert einen Cache für den angemeldeten Benutzer (da vertrauliche Clientanwendungen einen Cache pro Benutzer aufweisen sollten).

Ein Zugriffstoken und ein ID-Token werden im Wert `AuthenticationResult` zurückgegeben, während das neue Aktualisierungstoken im Cache gespeichert wird. Sie können diese Methode auch für verschiedene Integrationsszenarien verwenden, in denen ein Aktualisierungstoken verfügbar ist.

## <a name="v10-and-v20-tokens"></a>v1.0- und v2.0-Token

Es gibt zwei Tokenversionen: v1.0-Token und v2.0-Token. Der (von ADAL verwendete) v1.0-Endpunkt gibt v1.0-ID-Token aus. Der von (MSAL verwendete) v2.0-Endpunkt gibt dagegen v2.0-ID-Token aus. Beide Endpunkte geben jedoch Zugriffstoken der Tokenversion aus, die von der Web-API akzeptiert wird. Mithilfe einer Eigenschaft im Anwendungsmanifest der Web-API können Entwickler auswählen, welche Tokenversion akzeptiert wird. Informationen finden Sie unter `accessTokenAcceptedVersion` in der Referenzdokumentation zum [Anwendungsmanifest](reference-app-manifest.md).

Weitere Informationen zu v1.0- und v2.0-Zugriffstoken finden Sie im [Artikel zu Azure Active Directory-Zugriffstoken](access-tokens.md).

## <a name="exceptions"></a>Ausnahmen

### <a name="interaction-required-exceptions"></a>Ausnahmen, die eine Interaktion erfordern

In MSAL.NET erfassen Sie `MsalUiRequiredException`, wie in [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token) beschrieben.

```csharp
catch(MsalUiRequiredException exception)
{
 try {"try to authenticate interactively"}
}
```

Ausführliche Informationen finden Sie unter [Behandeln von Fehlern und Ausnahmen in MSAL.NET](msal-error-handling-dotnet.md).

In ADAL.NET gab es weniger explizite Ausnahmen. Beispiel: Wenn in ADAL ein Fehler bei der automatischen Authentifizierung auftrat, wurde die Ausnahme abgefangen und nach dem Fehlercode `user_interaction_required` gesucht:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Ausführliche Informationen finden Sie im [empfohlenen Muster zum Abrufen eines Tokens in öffentlichen Clientanwendungen](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) für ADAL.NET.

### <a name="prompt-behavior"></a>Verhalten der Eingabeaufforderung

Das Verhalten der Eingabeaufforderung in MSAL.NET entspricht dem Verhalten der Eingabeaufforderung in ADAL.NET:

|  ADAL.NET | MSAL.NET | BESCHREIBUNG |
| ----------- | ----------- | -------------|
| `PromptBehavior.Auto`| `NoPrompt`| Azure AD wählt das beste Verhalten aus (automatische Anmeldung von Benutzer*innen, wenn sie nur mit einem Konto angemeldet sind, oder Anzeige der Kontoauswahl, wenn sie mit mehreren Konten angemeldet sind). |
| `PromptBehavior.Always`| `ForceLogin` | Setzt das Anmeldefeld zurück und zwingt den*die Benutzer*in, seine bzw. ihre Anmeldeinformationen erneut einzugeben |
| `PromptBehavior.RefreshSession`| `Consent`| Erzwingt, das der*die Benutzer*in allen Berechtigungen noch mal zustimmt |
| `PromptBehavior.Never`| `Never`| Nicht verwenden; verwenden Sie stattdessen die [empfohlenen Muster für öffentliche Client-Apps](scenario-desktop-acquire-token.md?tabs=dotnet) |
| `PromptBehavior.SelectAccount`| `SelectAccount`| Zeigt die Kontoauswahl an und zwingt den*die Benutzer*in, ein Konto auszuwählen |

### <a name="handling-claim-challenge-exceptions"></a>Behandeln von Ausnahmen bei Anspruchsaufforderungen

Manchmal wird von Azure AD beim Abrufen eines Tokens eine Ausnahme ausgelöst, wenn eine Ressource zusätzliche Ansprüche vom Benutzer benötigt (etwa bei der zweistufigen Authentifizierung).

In MSAL.NET werden Ausnahmen bei Anspruchsaufforderungen auf folgende Weise behandelt:

- `Claims` werden in `MsalServiceException` ausgegeben.
- Es gibt eine Methode vom Typ `.WithClaim(claims)`, die auf die Builder vom Typ `AcquireTokenXXX` angewendet werden kann.

Ausführliche Informationen finden Sie unter [MsalUiRequiredException](msal-error-handling-dotnet.md#msaluirequiredexception).

In ADAL.NET wurden Ausnahmen bei Anspruchsaufforderungen auf folgende Weise behandelt:

- `AdalClaimChallengeException` ist eine (von `AdalServiceException` abgeleitete) Ausnahme. Das `Claims`-Element enthält ein JSON-Fragment mit den erwarteten Ansprüchen. 
- Die öffentliche Clientanwendung, die diese Ausnahme empfängt, musste die `AcquireTokenInteractive`-Überschreibung mit einem Anspruchsparameter aufrufen. Bei dieser Überschreibung von `AcquireTokenInteractive` wird der Cache gar nicht erst mit einbezogen, da dies nicht erforderlich ist. Das liegt daran, dass das Token im Cache nicht über die richtigen Ansprüche verfügt. (Andernfalls wäre keine Ausnahme vom Typ `AdalClaimChallengeException` ausgelöst worden.) Somit besteht keine Notwendigkeit, den Cache abzufragen. `ClaimChallengeException` kann in einer Web-API mit OBO empfangen werden. In einer öffentlichen Clientanwendung, die diese Web-API aufruft, muss jedoch `AcquireTokenInteractive` aufgerufen werden.

Ausführliche Informationen mit Beispielen finden Sie unter [Behandeln von „AdalClaimChallengeException“](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception).


## <a name="scopes"></a>Bereiche

ADAL verwendet das Konzept von Ressourcen mit Ressourcen-ID-Zeichenfolge (`resourceId`). MSAL.NET verwendet hingegen Bereiche. Von Azure AD wird die folgende Logik verwendet:

- Für ADAL (v1.0-Endpunkt) mit einem v1.0-Zugriffstoken (einzige Möglichkeit): `aud=resource`.
- Für MSAL (v2.0-Endpunkt) mit Anforderung eines Zugriffstokens für eine Ressource, die v2.0-Token akzeptiert: `aud=resource.AppId`.
- Für MSAL (v2.0-Endpunkt) mit Anforderung eines Zugriffstokens für eine Ressource, die ein v1.0-Zugriffstoken akzeptiert: Azure AD analysiert die gewünschte Zielgruppe anhand des angeforderten Bereichs. Hierzu wird alles vor dem letzten Schrägstrich als Ressourcenbezeichner verwendet. Wenn von `https://database.windows.net` also die Zielgruppe `https://database.windows.net/` erwartet wird, muss als Bereich `https://database.windows.net//.default` angefordert werden. (Beachten Sie den doppelten Schrägstrich vor „./default“.) Dies wird in den beiden folgenden Beispielen veranschaulicht:

### <a name="example-1"></a>Beispiel 1

Wenn Sie Token für eine Anwendung abrufen möchten, die v1.0-Token akzeptiert (z. B. die Microsoft Graph-API unter `https://graph.microsoft.com`), müssen Sie `scopes` erstellen, indem Sie einen gewünschten Ressourcenbezeichner mit einer gewünschten OAuth2-Berechtigung für die betreffende Ressource verketten.

Wenn Sie also beispielsweise über eine v1.0-Web-API mit dem App-ID-URI `ResourceId` auf den Namen des Benutzers zugreifen möchten, muss der folgende Code verwendet werden:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Wenn Sie mit MSAL.NET und Azure Active Directory Lese- und Schreibvorgänge über die Microsoft Graph-API (`https://graph.microsoft.com/`) ausführen möchten, müssen Sie eine Liste mit Bereichen erstellen, wie im folgenden Codeausschnitt zu sehen:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

### <a name="example-2"></a>Beispiel 2

Wenn die Ressourcen-ID mit einem Schrägstrich (/) endet, muss beim Schreiben des Bereichswerts ein doppelter Schrägstrich verwendet werden. Wenn Sie also beispielsweise den entsprechenden Bereich der Azure Resource Manager-API (`https://management.core.windows.net/`) schreiben möchten, fordern Sie den folgenden Bereich an. (Beachten Sie die beiden Schrägstriche.)

```csharp
var resource = "https://management.core.windows.net/"
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Dies ist erforderlich, weil die Resource Manager-API einen Schrägstrich im Zielgruppenanspruch (`aud`) erwartet und ein weiterer Schrägstrich erforderlich ist, um den API-Namen vom Bereich zu trennen.

Wenn Sie ein Token für alle statischen Bereiche einer v1.0-Anwendung abrufen möchten, müssen Sie Ihre Bereichsliste wie im folgenden Codeausschnitt erstellen:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

Bei einem Clientanmeldeinformations-Flow lautet der zu übergebende Bereich ebenfalls `/.default`. Dieser Bereich bedeutet für Azure AD: Alle Berechtigungen auf Anwendungsebene, denen der Administrator bei der Anwendungsregistrierung zugestimmt hat.

## <a name="next-steps"></a>Nächste Schritte

[Migrieren Ihrer Apps von ADAL zu MSAL](msal-net-migration.md)
[Migrieren Ihrer vertraulichen ADAL.NET-Client-Apps für die Verwendung von MSAL.NET](msal-net-migration-confidential-client.md)
