---
title: Anpassen der App-Ansprüche von Azure AD-Mandanten (PowerShell)
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Ansprüche anpassen, die in Token für eine Anwendung in einem bestimmten Azure Active Directory-Mandanten ausgegeben werden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 905a73a28e9a41370e80ef63d8212b6c323e46ed
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112553340"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>Anpassen von in Token ausgegebenen Ansprüchen für eine bestimmte App in einem Mandanten

Ein Anspruch (Claim) bezeichnet Informationen, die ein Identitätsanbieter über einen Benutzer in dem für diesen Benutzer ausgestellten Token angibt. Mandantenadministratoren passen in Token ausgegebene Ansprüche für eine bestimmte Anwendung in ihrem Mandanten an. Mithilfe von Richtlinien für die Anspruchszuordnung können Sie Folgendes:

- Auswählen, welche Ansprüche in Token enthalten sind.
- Anspruchstypen erstellen, die es noch nicht gibt.
- Die Quelle der in konkreten Ansprüchen ausgegebenen Daten auswählen oder ändern.

Die Anspruchsanpassung unterstützt das Konfigurieren von Richtlinien für die Zuordnung von Ansprüchen für die Protokolle WS-Fed, SAML, OAuth und OpenID Connect.

> [!NOTE]
> Diese Funktion ersetzt die über das Azure-Portal bereitgestellte [Anpassung von Ansprüchen](active-directory-saml-claims-customization.md). Wenn Sie in derselben Anwendung Ansprüche über das Portal anpassen, zusätzlich zur Microsoft Graph/PowerShell-Methode (in diesem Dokument erklärt), werden für diese Anwendung ausgestellte Token die Konfiguration im Portal ignorieren. Alle Konfigurationen, die mithilfe von in diesem Dokument aufgeführten Methoden vorgenommen werden, werden im Portal nicht übernommen.

In diesem Artikel führen wir Sie ausführlich durch einige allgemeine Szenarios, anhand derer veranschaulicht wird, wie Sie den [Richtlinientyp „Anspruchszuordnung“](reference-claims-mapping-policy-type.md) verwenden können.

## <a name="get-started"></a>Erste Schritte

In den folgenden Beispielen erstellen, aktualisieren, verknüpfen und löschen Sie Richtlinien für Dienstprinzipale. Anspruchszuordnungsrichtlinien können nur Dienstprinzipalobjekten zugewiesen werden. Wenn Azure AD neu für Sie ist, sollten Sie sich darüber informieren, wie Sie [einen Azure AD-Mandanten erhalten](quickstart-create-new-tenant.md), bevor Sie mit diesen Beispielen fortfahren.

Beim Erstellen einer Richtlinie für die Anspruchszuordnung können Sie auch einen Anspruch aus dem Erweiterungsattribut eines Verzeichnisschemas in Token ausgeben. Verwenden Sie *ExtensionID* für das Erweiterungsattribut anstelle von *ID* im `ClaimsSchema`-Element.  Weitere Informationen zu Erweiterungsattributen finden Sie unter [Verwenden von Erweiterungsattributen des Verzeichnisschemas](active-directory-schema-extensions.md).

> [!NOTE]
> Die [öffentliche Vorschauversion Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) ist erforderlich, um Richtlinien für die Anspruchszuordnung zu konfigurieren. Das PowerShell-Modul befindet sich in der Vorschau, während die Runtime für die Anspruchszuordnung und Tokenerstellung in Azure allgemein verfügbar ist. Für Updates des PowerShell-Vorschaumoduls müssen Sie möglicherweise Ihre Konfigurationsskripts aktualisieren oder ändern. 

Führen Sie die folgenden Schritte aus, um zu beginnen:

1. Laden Sie die aktuelle [Public Preview des Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) herunter.
1. Führen Sie den Befehl [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0-preview) aus, um sich in Ihrem Azure AD-Administratorkonto anzumelden. Führen Sie diesen Befehl bei jedem Start einer neuen Sitzung aus.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Führen Sie den folgenden Befehl aus, um alle Richtlinien anzuzeigen, die in Ihrer Organisation erstellt wurden. Es wird empfohlen, dass Sie diesen Befehl nach den meisten Vorgängen in den folgenden Szenarios ausführen, um zu überprüfen, dass Ihre Richtlinien wie erwartet erstellt wurden.

   ``` powershell
   Get-AzureADPolicy
   ```

Erstellen Sie als Nächstes eine Anspruchszuordnungsrichtlinie, und weisen Sie sie einem Dienstprinzipal zu.  In den folgenden Beispielen finden Sie allgemeine Szenarios:
- [Weglassen der grundlegenden Ansprüche aus Token](#omit-the-basic-claims-from-tokens)
- [Schließen Sie EmployeeID und TenantCountry als Ansprüche in Token ein](#include-the-employeeid-and-tenantcountry-as-claims-in-tokens)
- [Verwenden Sie eine Anspruchstransformation in Token](#use-a-claims-transformation-in-tokens)

Nachdem Sie eine Anspruchszuordnungsrichtlinie erstellt haben, konfigurieren Sie Ihre Anwendung so, dass bestätigt wird, dass Token benutzerdefinierte Ansprüche enthalten.  Weitere Informationen finden Sie unter [Überlegungen zur Sicherheit](#security-considerations).

## <a name="omit-the-basic-claims-from-tokens"></a>Weglassen der grundlegenden Ansprüche aus Token

In diesem Beispiel erstellen Sie eine Richtlinie, die den [grundlegenden Anspruchssatz](reference-claims-mapping-policy-type.md#claim-sets) aus den Token entfernt, die an den verknüpften Dienstprinzipal ausgestellte wurden.

1. Erstellen Sie eine Anspruchszuordnungsrichtlinie. Diese Richtlinie, verknüpft mit bestimmten Dienstprinzipalen, entfernt den grundlegenden Anspruchsatz aus den Token.
   1. Führen Sie diesen Befehl aus, um die Richtlinie zu erstellen:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie anzuzeigen und deren „ObjectId“ abzurufen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die „ObjectId“ Ihres Dienstprinzipals ab.
   1. Wenn Sie alle Dienstprinzipale Ihrer Organisation anzeigen möchten, können Sie [die Microsoft Graph-API abfragen](/graph/traverse-the-graph). Oder melden Sie sich im [Microsoft Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) bei Ihrem Azure AD-Konto an.
   2. Führen Sie den folgenden Befehl aus, wenn Sie über die ObjectId Ihres Dienstprinzipals verfügen:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Schließen Sie EmployeeID und TenantCountry als Ansprüche in Token ein

In diesem Beispiel erstellen Sie eine Richtlinie, die Token, die für verknüpfte Dienstprinzipale ausgestellt wurden, EmployeeID und TenantCountry hinzufügt. EmployeeID wird als Namensanspruchstyp in den SAML-Token und JWTs ausgegeben. „TenantCountry“ wird als Anspruchstyp für Land/Region in den SAML-Token und JWTs ausgegeben. In diesem Beispiel fahren wir mit dem Einschließen des grundlegenden Anspruchssatzes in Token fort.

1. Erstellen Sie eine Anspruchszuordnungsrichtlinie. Diese Richtlinie, die mit bestimmten Dienstprinzipalen verknüpft ist, fügt den Token die Ansprüche „EmployeeID“ und „TenantCountry“ hinzu.
   1. Führen Sie den folgenden Befehl aus, um die Richtlinie zu erstellen:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

      > [!WARNING]
      > Wenn Sie eine Anspruchszuordnungsrichtlinie für ein Verzeichniserweiterungsattribut definieren, verwenden Sie die `ExtensionID`-Eigenschaft anstelle der `ID`-Eigenschaft im Textkörper des `ClaimsSchema`-Arrays.

   2. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie anzuzeigen und deren „ObjectId“ abzurufen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die „ObjectId“ Ihres Dienstprinzipals ab.
   1. Wenn Sie alle Dienstprinzipale Ihrer Organisation anzeigen möchten, können Sie [die Microsoft Graph-API abfragen](/graph/traverse-the-graph). Oder melden Sie sich im [Microsoft Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) bei Ihrem Azure AD-Konto an.
   2. Führen Sie den folgenden Befehl aus, wenn Sie über die ObjectId Ihres Dienstprinzipals verfügen:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Verwenden Sie eine Anspruchstransformation in Token

In diesem Beispiel erstellen Sie eine Richtlinie, die einen benutzerdefinierten Anspruch „JoinedData“ an JWTs ausgibt, die für verknüpfte Dienstprinzipale ausgestellt werden. Dieser Anspruch enthält einen Wert, der durch das Verknüpfen der im Attribut „extensionattribute1“ im Benutzerobjekt mit „.sandbox“ gespeicherten Daten erstellt wird. In diesem Beispiel schließen wir den grundlegenden Anspruchssatz in Token aus.

1. Erstellen Sie eine Anspruchszuordnungsrichtlinie. Diese Richtlinie, die mit bestimmten Dienstprinzipalen verknüpft ist, fügt den Token die Ansprüche „EmployeeID“ und „TenantCountry“ hinzu.
   1. Führen Sie den folgenden Befehl aus, um die Richtlinie zu erstellen:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Führen Sie den folgenden Befehl aus, um Ihre neue Richtlinie anzuzeigen und deren „ObjectId“ abzurufen:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die „ObjectId“ Ihres Dienstprinzipals ab.
   1. Wenn Sie alle Dienstprinzipale Ihrer Organisation anzeigen möchten, können Sie [die Microsoft Graph-API abfragen](/graph/traverse-the-graph). Oder melden Sie sich im [Microsoft Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) bei Ihrem Azure AD-Konto an.
   2. Führen Sie den folgenden Befehl aus, wenn Sie über die ObjectId Ihres Dienstprinzipals verfügen:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Sicherheitshinweise

Anwendungen, die Token empfangen, basieren auf der Tatsache, dass die Anspruchswerte von Azure AD autoritativ ausgestellt werden und nicht manipuliert werden können. Wenn Sie jedoch den Tokeninhalt durch Anspruchszuordnungsrichtlinien ändern, sind diese Annahmen möglicherweise nicht mehr korrekt. Anwendungen müssen explizit bestätigen, dass Token vom Ersteller der Anspruchszuordnungsrichtlinie geändert wurden, um sich vor Anspruchszuordnungsrichtlinien zu schützen, die von böswilligen Akteuren erstellt wurden. Dafür gibt es folgende Möglichkeiten:

- [Konfigurieren eines benutzerdefinierten Signaturschlüssels](#configure-a-custom-signing-key)
- [Aktualisieren Sie alternativ das Anwendungsmanifest](#update-the-application-manifest), um zugeordnete Ansprüche zu akzeptieren.
 
Andernfalls gibt Azure AD einen [`AADSTS50146`Fehlercode](reference-aadsts-error-codes.md#aadsts-error-codes) zurück.

### <a name="configure-a-custom-signing-key"></a>Konfigurieren eines benutzerdefinierten Signaturschlüssels

Bei mehrinstanzenfähigen Apps sollte ein benutzerdefinierter Signaturschlüssel verwendet werden.  Legen Sie `acceptMappedClaims` nicht im App-Manifest fest. Wenn Sie eine App im Azure-Portal einrichten, erhalten Sie ein App-Registrierungsobjekt und einen Dienstprinzipal in Ihrem Mandanten.  Diese App verwendet den globalen Azure-Anmeldeschlüssel, der nicht zum Anpassen von Ansprüchen in Token verwendet werden kann.  Um benutzerdefinierte Ansprüche in Token abzurufen, erstellen Sie einen benutzerdefinierten Anmeldeschlüssel aus einem Zertifikat, und fügen Sie ihn dem Dienstprinzipal hinzu.  Sie können zu Testzwecken ein selbstsigniertes Zertifikat verwenden. Nach dem Konfigurieren des benutzerdefinierten Signaturschlüssels muss Ihr Anwendungscode den [Tokensignaturschlüssel überprüfen](#validate-token-signing-key).

Fügen Sie dem Dienstprinzipal die folgenden Informationen hinzu:

- Privater Schlüssel (als [Schlüsselanmeldeinformation](/graph/api/resources/keycredential))
- Kennwort (als [Kennwortanmeldeinformation](/graph/api/resources/passwordcredential))
- Öffentlicher Schlüssel (als [Schlüsselanmeldeinformation](/graph/api/resources/keycredential))

Extrahieren Sie den Base64-codierten privaten und öffentlichen Schlüssel aus dem PFX-Dateiexport Ihres Zertifikats. Stellen Sie sicher, dass die für „Sign“ verwendete `keyId` für `keyCredential` mit der `keyId` von `passwordCredential` übereinstimmt. Sie können `customkeyIdentifier` generieren, indem Sie den Hash des Zertifikatfingerabdrucks abrufen.

#### <a name="request"></a>Anforderung

Im Folgenden wird das Format der HTTP PATCH-Anforderung zum Hinzufügen eines benutzerdefinierten Signaturschlüssels zu einem Dienstprinzipal veranschaulicht.  Der Wert „key“ in der `keyCredentials`-Eigenschaft wird zur besseren Lesbarkeit gekürzt. Der Wert ist Base64-codiert. Für den privaten Schlüssel besitzt die Eigenschaft den Wert „Sign“. Für den öffentlichen Schlüssel besitzt die Eigenschaft den Wert „Verify“.

```
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json
Authorization: Bearer {token}

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=contoso"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=contoso"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "mypassword"
        }
    ]
}
```

#### <a name="configure-a-custom-signing-key-using-powershell"></a>Konfigurieren eines benutzerdefinierten Signaturschlüssels mit PowerShell

Verwenden Sie PowerShell, um eine [öffentliche MSAL-Clientanwendung zu instanziieren](msal-net-initializing-client-applications.md#initializing-a-public-client-application-from-code), und verwenden Sie die [Autorisierungscodegenehmigung](v2-oauth2-auth-code-flow.md), um ein Zugriffstoken für die delegierte Berechtigung für Microsoft Graph abzurufen. Verwenden Sie das Zugriffstoken, um Microsoft Graph aufzurufen, und konfigurieren Sie einen benutzerdefinierten Signaturschlüssel für den Dienstprinzipal. Nach dem Konfigurieren des benutzerdefinierten Signaturschlüssels muss Ihr Anwendungscode den [Tokensignaturschlüssel überprüfen](#validate-token-signing-key).

Sie benötigen folgende Komponenten, um das Skript auszuführen:
1. Die Objekt-ID des Dienstprinzipals Ihrer Anwendung, die sich auf dem Blatt **Übersicht** des Eintrags Ihrer Anwendung in [Unternehmensanwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) im Azure-Portal befindet
2. Eine App-Registrierung zum Anmelden eines Benutzers und Abrufen des Zugriffstokens zum Aufrufen der Microsoft Graph-API. Rufen Sie die Anwendungs-ID (Client-ID) der App im Azure-Portal über das Blatt **Übersicht** des Eintrags Ihrer Anwendung unter [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) ab. Die App-Registrierung sollte die folgende Konfiguration aufweisen:
    - Der Umleitungs-URI "http://localhost", der in der Plattformkonfiguration für **mobile Anwendungen und Desktopanwendungen** aufgeführt ist
    - Delegierte Microsoft Graph-Berechtigungen (**Application.ReadWrite.All** und **User.Read**), die unter **API-Berechtigungen** aufgeführt sind. Stellen Sie sicher, dass Sie diesen Berechtigungen Administratoreinwilligung gewähren.
3. Einen Benutzer, der sich anmeldet, um das Microsoft Graph-Zugriffstoken abzurufen. Der Benutzer sollte eine der folgenden Azure AD-Administratorrollen besitzen (erforderlich, um das Dienstprinzipal zu aktualisieren):
    - Cloudanwendungsadministrator
    - Anwendungsadministrator
    - Globaler Administrator
4. Ein Zertifikat, das als benutzerdefinierter Signaturschlüssel für unsere Anwendung konfiguriert werden soll. Sie können entweder ein selbstsigniertes Zertifikat erstellen oder eines von Ihrer vertrauenswürdigen Zertifizierungsstelle beziehen. Die folgenden Zertifikatkomponenten werden im Skript verwendet:
    - Öffentlicher Schlüssel (in der Regel eine CER-Datei)
    - Privater Schlüssel im PKCS#12-Format (in PFX-Datei)
    - Kennwort für den privaten Schlüssel (PFX-Datei)

> [!IMPORTANT]
> Der private Schlüssel muss im PKCS #12 sein, da Azure AD keine anderen Formattypen unterstützt. Die Verwendung des falschen Formats kann zum Fehler „Invalid certificate: Key value is invalid certificate“ (Ungültiges Zertifikat: Schlüsselwert ist ungültiges Zertifikat) führen, wenn Microsoft Graph verwendet wird, um den Dienstprinzipal mit einer `keyCredentials`-Klasse mit den Zertifikatinformationen zu patchen (PATCH).

```powershell

$fqdn="fourthcoffeetest.onmicrosoft.com" # this is used for the 'issued to' and 'issued by' field of the certificate
$pwd="mypassword" # password for exporting the certificate private key
$location="C:\\temp" # path to folder where both the pfx and cer file will be written to

# Create a self-signed cert
$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx"
 
# Export the public and private keys
Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile

$ClientID = "<app-id>"
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "fourthcoffeetest.onmicrosoft.com"
$redirectURL = "http://localhost" # this reply URL is needed for PowerShell Core 
[string[]] $Scopes = "https://graph.microsoft.com/.default"
$pfxpath = $pfxFile # path to pfx file
$cerpath = $cerFile # path to cer file
$SPOID = "<service-principal-id>"
$graphuri = "https://graph.microsoft.com/v1.0/serviceprincipals/$SPOID"
$password = $pwd  # password for the pfx file
 
 
# choose the correct folder name for MSAL based on PowerShell version 5.1 (.Net) or PowerShell Core (.Net Core)
 
if ($PSVersionTable.PSVersion.Major -gt 5)
    { 
        $core = $true
        $foldername =  "netcoreapp2.1"
    }
else
    { 
        $core = $false
        $foldername = "net45"
    }
 
# Load the MSAL/microsoft.identity/client assembly -- needed once per PowerShell session
[System.Reflection.Assembly]::LoadFrom((Get-ChildItem C:/Users/<username>/.nuget/packages/microsoft.identity.client/4.32.1/lib/$foldername/Microsoft.Identity.Client.dll).fullname) | out-null
  
$global:app = $null
  
$ClientApplicationBuilder = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($ClientID)
[void]$ClientApplicationBuilder.WithAuthority($("$loginURL/$tenantdomain"))
[void]$ClientApplicationBuilder.WithRedirectUri($redirectURL)
 
$global:app = $ClientApplicationBuilder.Build()
  
Function Get-GraphAccessTokenFromMSAL {
    [Microsoft.Identity.Client.AuthenticationResult] $authResult  = $null
    $AquireTokenParameters = $global:app.AcquireTokenInteractive($Scopes)
    [IntPtr] $ParentWindow = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
    if ($ParentWindow)
    {
        [void]$AquireTokenParameters.WithParentActivityOrWindow($ParentWindow)
    }
    try {
        $authResult = $AquireTokenParameters.ExecuteAsync().GetAwaiter().GetResult()
    }
    catch {
        $ErrorMessage = $_.Exception.Message
        Write-Host $ErrorMessage
    }
     
    return $authResult
}
  
$myvar = Get-GraphAccessTokenFromMSAL
if ($myvar)
{
    $GraphAccessToken = $myvar.AccessToken
    Write-Host "Access Token: " $myvar.AccessToken
    #$GraphAccessToken = "eyJ0eXAiOiJKV1QiL ... iPxstltKQ"
    
 
    #  this is for PowerShell Core
    $Secure_String_Pwd = ConvertTo-SecureString $password -AsPlainText -Force
 
    # reading certificate files and creating Certificate Object
    if ($core)
    {
        $pfx_cert = get-content $pfxpath -AsByteStream -Raw
        $cer_cert = get-content $cerpath -AsByteStream -Raw
        $cert = Get-PfxCertificate -FilePath $pfxpath -Password $Secure_String_Pwd
    }
    else
    {
        $pfx_cert = get-content $pfxpath -Encoding Byte
        $cer_cert = get-content $cerpath -Encoding Byte
        # Write-Host "Enter password for the pfx file..."
        # calling Get-PfxCertificate in PowerShell 5.1 prompts for password
        # $cert = Get-PfxCertificate -FilePath $pfxpath
        $cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($pfxpath, $password)
    }
 
    # base 64 encode the private key and public key
    $base64pfx = [System.Convert]::ToBase64String($pfx_cert)
    $base64cer = [System.Convert]::ToBase64String($cer_cert)
 
    # getting id for the keyCredential object
    $guid1 = New-Guid
    $guid2 = New-Guid
 
    # get the custom key identifier from the certificate thumbprint:
    $hasher = [System.Security.Cryptography.HashAlgorithm]::Create('sha256')
    $hash = $hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($cert.Thumbprint))
    $customKeyIdentifier = [System.Convert]::ToBase64String($hash)
 
    # get end date and start date for our keycredentials
    $endDateTime = ($cert.NotAfter).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
    $startDateTime = ($cert.NotBefore).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
 
    # building our json payload
    $object = [ordered]@{    
    keyCredentials = @(       
         [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid1
            startDateTime = $startDateTime 
            type = "X509CertAndPassword"
            usage = "Sign"
            key = $base64pfx
            displayName = "CN=fourthcoffeetest" 
        },
        [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid2
            startDateTime = $startDateTime 
            type = "AsymmetricX509Cert"
            usage = "Verify"
            key = $base64cer
            displayName = "CN=fourthcoffeetest"   
        }
        )  
    passwordCredentials = @(
        [ordered]@{
            customKeyIdentifier = $customKeyIdentifier
            keyId = $guid1           
            endDateTime = $endDateTime
            startDateTime = $startDateTime
            secretText = $password
        }
    )
    }
 
    $json = $object | ConvertTo-Json -Depth 99
    Write-Host "JSON Payload:"
    Write-Output $json
 
    # Request Header
    $Header = @{}
    $Header.Add("Authorization","Bearer $($GraphAccessToken)")
    $Header.Add("Content-Type","application/json")
 
    try 
    {
        Invoke-RestMethod -Uri $graphuri -Method "PATCH" -Headers $Header -Body $json
    } 
    catch 
    {
        # Dig into the exception to get the Response details.
        # Note that value__ is not a typo.
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__ 
        Write-Host "StatusDescription:" $_.Exception.Response.StatusDescription
    }
 
    Write-Host "Complete Request"
}
else
{
    Write-Host "Fail to get Access Token"
}
```

#### <a name="validate-token-signing-key"></a>Überprüfen des Tokensignaturschlüssels
Apps mit aktivierter Anspruchszuordnung müssen ihre Tokensignaturschlüssel überprüfen, indem sie `appid={client_id}` an ihre [OpenID Connect-Metadatenanforderungen](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) anfügen. Im Folgenden finden Sie das Format des OpenID Connect-Metadatendokuments, das Sie verwenden sollten:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Aktualisieren des Anwendungsmanifests

Alternativ können Sie für einzelne Mandantenanwendungen die `acceptMappedClaims`-Eigenschaft im [Anwendungsmanifest](reference-app-manifest.md) auf `true` festlegen.  Laut Dokumentation für den [apiApplication-Ressourcentyp](/graph/api/resources/apiapplication#properties) kann eine Anwendung die Anspruchszuordnung verwenden, ohne einen benutzerdefinierten Signaturschlüssel anzugeben. 

> [!WARNING]
> Legen Sie die `acceptMappedClaims`-Eigenschaft für mehrinstanzenfähige Apps nicht auf `true` fest. Dies kann dazu führen, dass böswillige Akteure Anspruchszuordnungsrichtlinien für Ihre App erstellen.

Dazu muss die angeforderte Tokenzielgruppe einen verifizierten Domänennamen Ihres Azure AD-Mandanten verwenden. Dies bedeutet, dass Sie `Application ID URI` (im Anwendungsmanifest durch `identifierUris` dargestellt) beispielsweise auf `https://contoso.com/my-api` oder `https://contoso.onmicrosoft.com/my-api` (Verwendung des Standardmandantennamens) festlegen.

Wenn Sie keine überprüfte Domäne verwenden, gibt Azure AD einen `AADSTS501461`-Fehlercode mit der Meldung aus, dass *AcceptMappedClaims nur für eine Tokenzielgruppe, die mit der Anwendungs-GUID übereinstimmt, oder einer Zielgruppe in den überprüften Domänen des Mandanten unterstützt wird, und Sie entweder den Ressourcenbezeichner ändern oder einen anwendungsspezifischen Signaturschlüssel verwenden müssen*.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie im Referenzartikel zum [Richtlinientyp „Anspruchszuordnung“](reference-claims-mapping-policy-type.md).
- Weitere Informationen dazu, wie Sie über das Azure-Portal die im SAML-Token ausgestellten Ansprüche anpassen, finden Sie unter [Vorgehensweise: Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](active-directory-saml-claims-customization.md)
- Weitere Informationen zu Erweiterungsattributen finden Sie unter [Verwenden von Erweiterungsattributen des Verzeichnisschemas in Ansprüchen](active-directory-schema-extensions.md).
