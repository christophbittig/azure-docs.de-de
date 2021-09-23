---
title: Verwenden einer verwalteten Identität mit einer Anwendung
description: Verwenden von verwalteten Identitäten in Azure Service Fabric-Anwendungscode für den Zugriff auf Azure-Dienste.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: cc7eff8119e6b79ca991543cdc09cfe106989fd3
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867057"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Nutzen der verwalteten Identität einer Service Fabric-Anwendung für den Zugriff auf Azure-Dienste

Service Fabric-Anwendungen können verwaltete Identitäten für den Zugriff auf andere Azure-Ressourcen verwenden, die die Azure Active Directory-basierte Authentifizierung unterstützen. Eine Anwendung kann ein [Zugriffstoken](../active-directory/develop/developer-glossary.md#access-token) abrufen, das ihre system- oder benutzerseitig zugewiesene Identität repräsentiert. Die Anwendung kann dieses Token als Bearertoken verwenden, um sich bei einem anderen Dienst zu authentifizieren – dieser wird auch als [geschützter Ressourcenserver](../active-directory/develop/developer-glossary.md#resource-server) bezeichnet. Das Token repräsentiert die Identität, die der Service Fabric-Anwendung zugewiesen wurde, und wird nur für Azure-Ressourcen (einschließlich Service Fabric-Anwendungen) ausgestellt, die diese Identität gemeinsam nutzen. Eine detaillierte Beschreibung verwalteter Identitäten sowie Informationen zur Unterscheidung zwischen system- und benutzerseitig zugewiesenen Identitäten finden Sie in der [Übersicht über verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md). Im vorliegenden Artikel wird eine Service Fabric-Anwendung, die verwaltete Identitäten nutzen kann, als [Clientanwendung](../active-directory/develop/developer-glossary.md#client-application) bezeichnet.

Sehen Sie sich eine begleitende Beispielanwendung an, die die Verwendung von durch das System und Benutzer zugewiesenen [verwalteten Service Fabric-Anwendungsidentitäten](https://github.com/Azure-Samples/service-fabric-managed-identity) mit Reliable Services und Containern zeigt.

> [!IMPORTANT]
> Eine verwaltete Identität stellt die Zuordnung zwischen einer Azure-Ressource und einem Dienstprinzipal im entsprechenden Azure AD-Mandanten dar, der dem Abonnement zugeordnet ist, das die Ressource enthält. Im Kontext von Service Fabric werden verwaltete Identitäten daher nur für Anwendungen unterstützt, die als Azure-Ressourcen bereitgestellt wurden. 

> [!IMPORTANT]
> Der Clientanwendung muss Zugriff auf die geschützte Ressource gewährt werden, bevor diese Anwendung die verwaltete Identität einer Service Fabric-Anwendung nutzen kann. Informationen zur Unterstützung finden Sie in der Liste der [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources). In der Dokumentation des jeweiligen Diensts finden Sie dann die spezifischen Schritte, um einer Identität Zugriff auf relevante Ressourcen zu gewähren. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Nutzen einer verwalteten Identität mit Azure.Identity

Das Azure Identity SDK unterstützt jetzt Service Fabric. Durch die Verwendung von Azure.Identity wird das Schreiben von Code für die Verwendung verwalteter Identitäten der Service Fabric-App vereinfacht, da das SDK das Abrufen von Token, das Zwischenspeichern von Token sowie die Serverauthentifizierung verarbeitet. Beim Zugriff auf die meisten Azure-Ressourcen wird das Konzept eines Tokens verborgen.

Service Fabric-Unterstützung ist in den folgenden Versionen für diese Sprachen verfügbar: 
- [C# in Version 1.3.0](https://www.nuget.org/packages/Azure.Identity). Ein [C#-Beispiel finden Sie hier](https://github.com/Azure-Samples/service-fabric-managed-identity).
- [Python in Version 1.5.0](https://pypi.org/project/azure-identity/). Ein [Python-Beispiel finden Sie hier](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Java in Version 1.2.0](/java/api/overview/azure/identity-readme).

C#-Beispiel für das Initialisieren von Anmeldeinformationen und Verwenden der Anmeldeinformationen zum Abrufen eines Geheimnisses aus Azure Key Vault:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Abrufen eines Zugriffstokens mithilfe der REST-API
In Clustern, die für die Nutzung verwalteter Identitäten eingerichtet wurden, macht die Service Fabric-Runtime einen Localhost-Endpunkt verfügbar, den Anwendungen zum Abrufen von Zugriffstoken verwenden können. Der Endpunkt ist auf jedem Knoten im Cluster verfügbar, und alle Entitäten auf den Knoten können darauf zugreifen. Autorisierte Aufrufer können Zugriffstoken durch Aufrufen dieses Endpunkts und Bereitstellen eines Authentifizierungscodes abrufen. Der Code wird von der Service Fabric-Runtime für jede einzelne Aktivierung eines Dienstcodepakets generiert und ist an die Lebensdauer des Prozesses gebunden, der dieses Dienstcodepaket hostet.

Die Umgebung eines Service Fabric-Diensts, der verwaltete Identitäten nutzen kann, wird zu Beginn mit folgenden Variablen eingerichtet:
- IDENTITY_ENDPOINT: der Localhost-Endpunkt, der der verwalteten Identität des Diensts entspricht.
- IDENTITY_HEADER: Ein eindeutiger Authentifizierungscode, der den Dienst auf dem aktuellen Knoten darstellt.
- IDENTITY_SERVER_THUMBPRINT: Der Fingerabdruck des verwalteten Service Fabric-Identitätsservers.

> [!IMPORTANT]
> Der Anwendungscode sollte den Wert der Umgebungsvariablen IDENTITY_HEADER als vertraulich behandeln. Dieser Wert darf nicht protokolliert oder anderweitig verteilt werden. Der Authentifizierungscode hat außerhalb des lokalen Knotens oder nach Beendigung des Prozesses, der den Dienst hostet, keinen Wert, aber er repräsentiert die Identität des Service Fabric-Diensts und sollte daher mit denselben Vorsichtsmaßnahmen behandelt werden wie das Zugriffstoken selbst.

Zum Abrufen eines Tokens führt der Client die folgenden Schritte aus:
- Erstellen eines URI durch Verketten des Endpunkts der verwalteten Identität (Wert von IDENTITY_ENDPOINT) mit der API-Version und der Ressource (Zielgr.uppe), die für das Token erforderlich ist.
- Erstellen einer GET HTTP(S)-Anforderung für den angegebenen URI.
- Hinzufügen entsprechender Logik zur Überprüfung des Serverzertifikats.
- Hinzufügen des Authentifizierungscodes (Wert von IDENTITY_HEADER) als Header zur Anforderung.
- Übermitteln der Anforderung

Eine erfolgreiche Antwort enthält eine JSON-Nutzlast, die das resultierende Zugriffstoken repräsentiert, sowie Metadaten mit einer Beschreibung. Eine nicht erfolgreiche Antwort enthält auch eine Erläuterung des Fehlers. Weitere Informationen zur Fehlerbehandlung finden Sie im Folgenden.

Zugriffstoken werden von Service Fabric auf verschiedenen Ebenen zwischengespeichert (Knoten, Cluster, Ressourcenanbieterdienst), daher impliziert eine erfolgreiche Antwort nicht notwendigerweise, dass das Token direkt als Reaktion auf die Anforderung der Benutzeranwendung ausgestellt wurde. Token werden für einen kürzeren Zeitraum als ihre Lebensdauer zwischengespeichert, sodass garantiert ist, dass eine Anwendung ein gültiges Token empfängt. Es empfiehlt sich, dass der Anwendungscode jedes abgerufene Zugriffstoken selbst zwischenspeichert. Der Cacheschlüssel sollte die Zielgruppe (bzw. eine Ableitung davon) enthalten. 

Beispiel für eine Anforderung:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
Dabei gilt:

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `GET` | Das HTTP-Verb, mit dem angegeben wird, dass Sie Daten vom Endpunkt abrufen möchten. In diesem Fall ist dies ein OAuth-Zugriffstoken. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Der Endpunkt für verwaltete Identitäten für Service Fabric-Anwendungen, bereitgestellt über die IDENTITY_ENDPOINT-Umgebungsvariable. |
| `api-version` | Ein Abfragezeichenfolgenparameter, der die API-Version des Tokendiensts für verwaltete Identitäten angibt. Zurzeit ist `2019-07-01-preview` der einzige akzeptierte Wert. Änderungen vorbehalten. |
| `resource` | Ein Abfragezeichenfolgenparameter, der den App-ID-URI der Zielressource angibt. Dieser wird als `aud`-Anspruch (Zielgruppe) des ausgestellten Tokens angezeigt. Dieses Beispiel fordert ein Token für den Zugriff auf eine Azure Key Vault-Instanz mit dem App-ID-URI https:\//vault.azure.net/ an. |
| `Secret` | Ein HTTP-Anforderungsheaderfeld, das für den Service Fabric-Tokendienst für verwaltete Identitäten erforderlich ist, damit Service Fabric-Dienste den Aufrufer authentifizieren können. Dieser Wert wird von der Service Fabric-Runtime über die Umgebungsvariable IDENTITY_HEADER bereitgestellt. |


Beispiel für eine Antwort:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
Dabei gilt:

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `token_type` | Der Typ des Tokens, in diesem Fall ein Zugriffstoken des Typs „Bearer“. Dies bedeutet, dass das Element, das dieses Token vorlegt (der „Bearer“), der beabsichtigte Antragsteller des Tokens ist. |
| `access_token` | Das angeforderte Zugriffstoken. Beim Aufrufen einer geschützten REST-API wird das Token als „Bearertoken“ in das `Authorization`-Anforderungsheader-Feld eingebettet, damit der Aufrufer von der API authentifiziert werden kann. | 
| `expires_on` | Der Zeitstempel des Ablaufs des Zugriffstokens. Dieser wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ dargestellt und entspricht dem `exp`-Anspruch des Tokens. In diesem Fall lautet der Ablaufzeitstempel des Tokens 2019-08-08T06:10:11+00:00 (in RFC 3339).|
| `resource` | Die Ressource, für die das Zugriffstoken ausgestellt wurde, angegeben über den Abfragezeichenfolgenparameter `resource` der Anforderung. Entspricht dem aud-Anspruch des Tokens. |


## <a name="acquiring-an-access-token-using-c"></a>Abrufen eines Zugriffstokens mithilfe von C#
Das oben erläuterte Beispiel sieht in C# folgendermaßen aus:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Zugreifen auf Key Vault über eine Service Fabric-Anwendung mithilfe einer verwalteten Identität
Dieses Beispiel baut auf dem oben erläuterten auf und veranschaulicht den Zugriff mithilfe einer verwalteten Identität auf ein Geheimnis, das in einer Key Vault-Instanz gespeichert ist.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Fehlerbehandlung
Das Statuscodefeld des HTTP-Anforderungsheaders gibt den Erfolgsstatus der Anforderung an: Der Status „200 OK“ weist auf eine erfolgreiche Ausführung hin, und die Antwort enthält das Zugriffstoken, wie oben beschrieben. Im Folgenden finden Sie eine kurze Auflistung möglicher Fehlerantworten.

| Statuscode | Fehlerursache | Fehlerbehandlung |
| ----------- | ------------ | ------------- |
| 404 – Nicht gefunden. | Es liegt ein unbekannter Authentifizierungscode vor, oder der Anwendung wurde keine verwaltete Identität zugewiesen. | Korrigieren Sie das Setup der Anwendung oder den Code für den Tokenabruf. |
| 429: Zu viele Anforderungen. |  Der von Azure Active Directory oder Service Fabric auferlegte Drosselungsgrenzwert wurde erreicht. | Wiederholungsversuch mit exponentiellem Backoff. Siehe Anleitung unten. |
| 4xx – Fehler in der Anforderung. | Mindestens einer der Anforderungsparameter war falsch. | Wiederholen Sie den Vorgang nicht.  Überprüfen Sie den Fehler, um weitere Informationen zu erhalten.  4xx-Fehler sind Fehler während der Entwurfszeit.|
| 5xx-Fehler aus dem Dienst. | Das Subsystem der verwalteten Identität oder Azure Active Directory hat einen vorübergehenden Fehler zurückgegeben. | Der Vorgang kann nach kurzer Zeit wiederholt werden. Möglicherweise tritt beim erneuten Versuch eine Drosselungsbedingung (429) auf.|

Wenn ein Fehler auftritt, enthält der entsprechende HTTP-Antworttext ein JSON-Objekt mit den Fehlerdetails:

| Element | BESCHREIBUNG |
| ------- | ----------- |
| code | Fehlercode |
| correlationId | Eine Korrelations-ID, die zum Debuggen verwendet werden kann. |
| message | Ausführliche Beschreibung des Fehlers. **Fehlerbeschreibungen können sich jederzeit ändern. Verlassen Sie sich nicht auf die Fehlermeldung selbst.**|

Beispielfehler:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Im Folgenden finden Sie eine Liste typischer Service Fabric-Fehler speziell für verwaltete Identitäten:

| Code | `Message` | BESCHREIBUNG | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Das Geheimnis wurde in den Anforderungsheadern nicht gefunden. | Der Authentifizierungscode wurde nicht mit der Anforderung bereitgestellt. | 
| ManagedIdentityNotFound | Für den angegebenen Anwendungshost wurde keine verwaltete Identität gefunden. | Die Anwendung besitzt keine Identität, oder der Authentifizierungscode ist unbekannt. |
| ArgumentNullOrEmpty | Der Parameter „Ressource“ darf nicht NULL oder eine leere Zeichenfolge sein. | Die Ressource (Zielgruppe) wurde in der Anforderung nicht angegeben. |
| InvalidApiVersion | Die angegebene API-Version wird nicht unterstützt. Die unterstützte Version lautet „2019-07-01-preview“. | Fehlende oder nicht unterstützte API-Version im Anforderungs-URI. |
| InternalServerError | Ein Fehler ist aufgetreten. | Im Subsystem der verwalteten Identität ist ein Fehler aufgetreten, möglicherweise außerhalb des Service Fabric-Stapels. Die wahrscheinlichste Ursache ist ein falscher Wert für die Ressource (suchen Sie nach nachstehenden Schrägstrichen „/“). | 

## <a name="retry-guidance"></a>Informationen zur Wiederholung 

In der Regel ist der einzige Fehler, bei dem sich ein erneuter Versuch lohnt, der Fehler 429 (Zu viele Anforderungen). Bei internen Serverfehlern oder 5xx-Fehlercodes kann ein erneuter Versuch unternommen werden, aber die Ursache ist möglicherweise dauerhaft. 

Drosselungsgrenzwerte gelten für die Anzahl von Aufrufen, die vom Subsystem der verwalteten Identität empfangen werden – insbesondere für die Upstreamabhängigkeiten (den Azure-Dienst für verwaltete Identitäten oder den sicheren Tokendienst). Service Fabric speichert Token auf verschiedenen Ebenen in der Pipeline zwischen. Angesichts der verteilten Natur der beteiligten Komponenten erhält der Aufrufer möglicherweise inkonsistente Drosselungsantworten (beispielsweise kann bei der Anforderung eines Tokens für ein und dieselbe Identität auf einem Knoten bzw. in einer Instanz einer Anwendung eine Drosselung erfolgen, nicht jedoch auf einem anderen Knoten). Wenn die Drosselungsbedingung festgelegt ist, treten bei nachfolgenden Anforderungen aus der gleichen Anwendung möglicherweise Fehler mit den HTTP-Statuscode 429 (Zu viele Anforderungen) auf, bis die Bedingung bereinigt wird.  

Es empfiehlt sich, die Anforderungen, die aufgrund einer Drosselung nicht ausgeführt werden konnten, mit einem exponentiellen Backoff auf folgende Weise erneut zu versuchen: 

| Aufrufindex | Aktion bei Empfang von 429 | 
| --- | --- | 
| 1 | 1 Sekunde warten und erneut versuchen |
| 2 | 2 Sekunden warten und erneut versuchen |
| 3 | 4 Sekunden warten und erneut versuchen |
| 4 | 8 Sekunden warten und erneut versuchen |
| 4 | 8 Sekunden warten und erneut versuchen |
| 5 | 16 Sekunden warten und erneut versuchen |

## <a name="resource-ids-for-azure-services"></a>Ressourcen-IDs für Azure-Dienste
Eine Liste mit Ressourcen, die Azure AD unterstützen, und den dazugehörigen Ressourcen-IDs finden Sie unter [Azure-Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen einer Service Fabric-Anwendung mit verwalteter Identität in einem verwalteten Cluster](how-to-managed-cluster-application-managed-identity.md)
* [Bereitstellen einer Service Fabric-Anwendung mit einer systemseitig zugewiesenen verwalteten Identität in einem klassischen Cluster](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Bereitstellen einer Service Fabric-Anwendung mit einer benutzerseitig zugewiesenen verwalteten Identität in einem klassischen Cluster](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Gewähren des Zugriffs der verwalteten Identität einer Service Fabric-Anwendung auf Azure-Ressourcen](./how-to-grant-access-other-resources.md)
* [Untersuchen einer Beispielanwendung mit der verwalteten Service Fabric-Identität](https://github.com/Azure-Samples/service-fabric-managed-identity)