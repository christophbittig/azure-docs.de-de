---
title: Automatische Installation des Azure Active Directory-Anwendungsproxyconnectors
description: Hier wird erläutert, wie Sie eine unbeaufsichtigte Installation des Azure Active Directory-Anwendungsproxyconnectors durchführen, um sicheren Remotezugriff auf Ihre lokalen Apps zu gewähren.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 657588c9f74bd2a7b4da8acb73c4dc40aa987baf
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988922"
---
# <a name="create-an-unattended-installation-script-for-the-azure-active-directory-application-proxy-connector"></a>Erstellen eines Skripts für die unbeaufsichtigte Installation des Azure Active Directory-Anwendungsproxyconnectors

In diesem Thema wird das Erstellen eines Windows PowerShell-Skripts erläutert, das eine unbeaufsichtigte Installation und Registrierung des Azure AD-Anwendungsproxyconnectors ermöglicht.

Diese Funktion ist in folgenden Fällen nützlich:

* Installieren des Connectors auf Windows-Servern, auf denen keine Benutzeroberfläche aktiviert ist oder auf die nicht per Remotedesktop zugegriffen werden kann.
* Gleichzeitiges Installieren und Registrieren vieler Connectors.
* Integrieren der Connectorinstallation und Registrierung als Teil einer anderen Prozedur.
* Erstellen eines standardmäßigen Serverimages, das die Connectorbits enthält, aber nicht registriert ist.

Damit der [Anwendungsproxyconnector](application-proxy-connectors.md) funktioniert, muss er von einem Anwendungsadministrator mit Kennwort im Azure AD-Verzeichnis registriert werden. Diese Informationen werden normalerweise bei der Installation des Connectors in einem Popupdialogfeld eingegeben. Der Prozess kann jedoch mithilfe von PowerShell automatisiert werden.

Eine unbeaufsichtigte Installation umfasst zwei Schritte. Erstens: Die Installation des Connectors. Zweitens: Die Registrierung des Connectors bei Azure AD.

> [!IMPORTANT]
> Wenn Sie den Connector für die Azure Government-Cloud installieren, überprüfen Sie die [Voraussetzungen](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) und [Installationsschritte](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Zum Ausführen der Installation sind der Zugriff auf einen anderen Satz von URLs und ein zusätzlicher Parameter erforderlich.

## <a name="install-the-connector"></a>Installieren des Connectors
Gehen Sie wie folgt vor, um den Connector zu installieren, ohne ihn zu registrieren:

1. Öffnen Sie eine Eingabeaufforderung.
2. Führen Sie den folgenden Befehl aus. („/q“ steht hierbei für die automatische Installation.) Bei einer automatischen Installation werden Sie nicht zum Akzeptieren der Lizenzbedingungen aufgefordert.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Registrieren des Connectors bei Azure AD
Es gibt zwei Methoden zum Registrieren des Connectors:

* Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekt
* Registrieren des Connectors mithilfe eines offline erstellten Tokens

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekt
1. Erstellen Sie ein Windows PowerShell-Anmeldeinformationsobjekt (`$cred`) mit einem Administratorbenutzernamen und einem Kennwort für Ihr Verzeichnis. Führen Sie den folgenden Befehl aus, wobei Sie *\<username\>* und *\<password\>* ersetzen:

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Wechseln Sie zu **C:\Programme\Microsoft AAD App Proxy Connector**, und führen Sie das folgende Skript unter Verwendung des zuvor erstellten Objekts `$cred` aus:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrieren des Connectors mithilfe eines offline erstellten Tokens
1. Erstellen Sie mithilfe der AuthenticationContext-Klasse ein Offlinetoken unter Verwendung der Werte im folgenden Codeausschnitt bzw. in den PowerShell-Cmdlets:

   **Mithilfe von C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Mithilfe von PowerShell:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Sobald das Token vorliegt, erstellen Sie SecureString mit diesem Token:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Führen Sie folgenden Windows PowerShell-Befehl aus, und ersetzen Sie \<tenant GUID\> dabei durch die ID Ihres Verzeichnisses:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Nächste Schritte
* [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](application-proxy-configure-custom-domain.md)
* [Aktivieren der einmaligen Anmeldung](application-proxy-configure-single-sign-on-with-kcd.md)
* [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)