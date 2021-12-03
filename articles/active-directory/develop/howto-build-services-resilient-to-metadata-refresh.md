---
title: 'Wie gemacht: Erstellen Sie Dienste, die resistent sind gegenüber der Aktualisierung der OpenID Connect-Metadaten von Azure AD | Azure'
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie sicherstellen, dass Ihre Web-App oder Web-Api gegenüber der OpenID Connect-Metadaten-Aktualisierung von Azure AD resistent ist.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: jmprieur
ms.reviewer: marsma, shermanouko
ms.custom: aaddev
ms.openlocfilehash: 2b698f351198daeceddc6b254eb62ddb5f58a008
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988898"
---
# <a name="build-services-that-are-resilient-to-azure-ads-openid-connect-metadata-refresh"></a>Erstellen von Diensten, die gegenüber der Aktualisierung der OpenID Connect-Metadaten von Azure AD resilient sind

Geschützte Web-APIs müssen Zugriffstoken überprüfen. Web-Apps überprüfen auch die ID-Token. Die Token-Validierung besteht aus mehreren Teilen, wobei geprüft wird, ob das Token zur Anwendung gehört, von einem vertrauenswürdigen Identitätsanbieter (IDP) ausgestellt wurde, eine noch gültige Lebensdauer hat und nicht manipuliert wurde. Es können auch spezielle Validierungen vorliegen. Zum Beispiel muss die App die Signatur validieren und sicherstellen, dass Signierschlüssel (wenn sie in einem Token eingebettet sind) vertrauenswürdig sind und dass das Token nicht wiedergegeben wird. Wenn die Signaturschlüssel nicht in das Token eingebettet sind, müssen sie vom Identitätsanbieter (Discovery oder Metadaten) abgerufen werden. Manchmal ist es auch erforderlich, Schlüssel dynamisch zur Ausführungszeit abzurufen.

Web-Apps und Web-APIs müssen veraltete OpenID Connect Metadaten aktualisieren, damit sie robust sind. Dieser Artikel enthält Anleitungen zum Erzielen robuster Apps. Dies gilt für ASP.NET Core, ASP.NET classic und Microsoft.IdentityModel.

## <a name="aspnet-core"></a>ASP.NET Core

Verwenden Sie die neueste Version des [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) und befolgen Sie manuell die folgenden Richtlinien.

Stellen Sie in der `ConfigureServices`Methode der Datei Startup.cs sicher, dass`JwtBearerOptions.RefreshOnIssuerKeyNotFound` diese auf wahr gesetzt ist, und dass Sie die neueste Microsoft.IdentityModel.*-Bibliothek verwenden. Diese Eigenschaft sollte standardmäßig aktiviert sein.

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    …
    // shouldn’t be necessary as it’s true by default
    options.RefreshOnIssuerKeyNotFound = true;
    …
};
```

## <a name="aspnet-owin"></a>ASP.NET/OWIN

Microsoft empfiehlt, dass Sie auf ASP.NET Core umsteigen, da die Entwicklung von ASP.NET eingestellt wurde.

Wenn Sie ASP.NET classic verwenden, verwenden Sie das aktuellste [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel).

OWIN bietet ein automatisches Aktualisierungsintervall von 24 Stunden für den `OpenIdConnectConfiguration`. Diese Aktualisierung wird nur ausgelöst, wenn eine Anforderung nach Ablauf der 24-Stunden-Zeitspanne empfangen wird. Soweit uns bekannt ist, besteht keine Möglichkeit, diesen Wert zu ändern oder eine vorzeitige Aktualisierung auszulösen, abgesehen von einem Neustart der Anwendung.

## <a name="microsoftidentitymodel"></a>Microsoft.IdentityModel

Wenn Sie Ihr Token selbst validieren, z. B. in einer Azure-Funktion, verwenden Sie die aktuellste Version von [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) und befolgen Sie die Anleitung für Metadaten, die in den folgenden Codeschnipseln dargestellt ist.

```csharp
var configManager =
  new ConfigurationManager<OpenIdConnectConfiguration>(
    "http://someaddress.com",
    new OpenIdConnectConfigurationRetriever());

var config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
var validationParameters = new TokenValidationParameters()
{
  …
  IssuerSigningKeys = config.SigningKeys;
  …
}

var tokenHandler = new JsonWebTokenHandler();
result = Handler.ValidateToken(jwtToken, validationParameters);
if (result.Exception != null && result.Exception is SecurityTokenSignatureKeyNotFoundException)
{
  configManager.RequestRefresh();
  config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
  validationParameters = new TokenValidationParameters()
  {
    …
    IssuerSigningKeys = config.SigningKeys,
    …
  };

  // attempt to validate token again after refresh
  result = Handler.ValidateToken(jwtToken, validationParameters);
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Token-Validierung in einer geschützten Web-API](scenario-protected-web-api-app-configuration.md#token-validation)
