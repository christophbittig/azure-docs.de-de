---
title: Instanziieren einer öffentlichen Client-App (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie unter Verwendung der Microsoft Authentication Library für .NET (MSAL.NET) eine öffentliche Clientanwendung mit Konfigurationsoptionen instanziieren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 57d7cba072e51483732d103d358910ba443a525a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059412"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instanziieren einer öffentlichen Clientanwendung mit Konfigurationsoptionen unter Verwendung von MSAL.NET

In diesem Artikel wird beschrieben, wie Sie mithilfe der Microsoft-Authentifizierungsbibliothek für .NET (Microsoft Authentication Library for .NET, MSAL.NET) eine [öffentliche Clientanwendung](msal-client-applications.md) instanziieren.  Die Anwendung wird mit Konfigurationsoptionen instanziiert, die in einer Einstellungsdatei definiert werden.

Vor der Initialisierung einer Anwendung müssen Sie diese zunächst [registrieren](quickstart-register-app.md), damit Ihre App in Microsoft Identity Platform integriert werden kann. Nach der Registrierung benötigen Sie unter Umständen die folgenden Informationen (die Sie im Azure-Portal finden können):

- Die Client-ID (eine Zeichenfolge, die eine GUID darstellt)
- Die URL des Identitätsanbieters (Namensgeber der Instanz) und die Anmeldezielgruppe für Ihre Anwendung. Diese beiden Parameter werden zusammen als Autorität bezeichnet.
- Die Mandanten-ID, wenn Sie eine Geschäftsanwendung ausschließlich für Ihre Organisation schreiben (auch als Einzelmandantenanwendung bezeichnet).
- Für Web-Apps und gelegentlich auch für öffentliche Clientanwendungen (insbesondere, wenn Ihre App einen Broker verwenden muss) müssen Sie auch den Umleitungs-URI festlegen, mit dem der Identitätsanbieter Ihrer Anwendung die Sicherheitstoken sendet.

## <a name="default-reply-uri"></a>Standardantwort URI

In MSAL.NET 4.1+ kann die Standardumleitungs-URI (Antwort-URI) jetzt mit der Methode `public PublicClientApplicationBuilder WithDefaultRedirectUri()` festgelegt werden. Diese Methode setzt die Einstellung Umleitungs-URI der öffentlichen Kundenanwendung auf den empfohlenen Standardwert.

Das Verhalten dieser Methode ist abhängig von der Plattform, die Sie gerade verwenden. In der folgenden Tabelle wird beschrieben, welcher Umleitungs-URI auf bestimmten Plattformen festgelegt ist:

Plattform  | Umleitungs-URI  
---------  | --------------
Desktop-App (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
UWP | Wert von `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`
.NET Core | `http://localhost`

Für die UWP-Plattform wird das Erlebnis durch die Aktivierung von SSO mit dem Browser verbessert, indem der Wert auf das Ergebnis von `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` gesetzt wird. 

Für .NET Core setzt MSAL.Net den Wert auf den lokalen Host, damit der Benutzer den Systembrowser für die interaktive Authentifizierung verwenden kann.

> [!NOTE]
> Bei eingebetteten Browsern in Desktop-Szenarien wird die verwendete Umleitungs-URI von MSAL abgefangen, um zu erkennen, ob eine Antwort vom Identitätsanbieter zurückgegeben wird, ob ein Authentifizierungscode zurückgeschickt wurde. Diese URI kann daher in jeder Cloud verwendet werden, ohne dass eine tatsächliche Weiterleitung zu dieser URI erfolgt. Das heißt, Sie können und sollten `https://login.microsoftonline.com/common/oauth2/nativeclient` in jeder Cloud verwenden. Wenn Sie möchten, können Sie auch jede andere URI verwenden, solange Sie die Umleitungs-URI in MSAL und in der App-Registrierung korrekt konfigurieren. Die Angabe der Standard-URI in der Anwendungsregistrierung hat zur Folge, dass der Aufwand für die Einrichtung in MSAL am geringsten ist.


Eine .NET Core-Konsolenanwendung könnte beispielsweise die folgende *appsettings.json*-Konfigurationsdatei aufweisen:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Der folgende Code liest diese Datei mit dem .NET-Konfigurationsframework:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Der folgende Code erstellt Ihre Anwendung und verwendet dabei die Konfiguration aus der Einstellungsdatei:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```
