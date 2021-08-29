---
title: Zugreifen auf Benutzerinformationen in Azure Static Web Apps
description: Hier erfahren Sie, wie Sie vom Autorisierungsanbieter zurückgegebene Benutzerdaten lesen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 14f44504e48279d0a8bd8f8e95e98113a4647023
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515252"
---
# <a name="accessing-user-information-in-azure-static-web-apps"></a>Zugreifen auf Benutzerinformationen in Azure Static Web Apps

Von Azure Static Web Apps werden authentifizierungsbezogene Benutzerinformationen über einen [Direktzugriffsendpunkt](#direct-access-endpoint) und für [API-Funktionen](#api-functions) bereitgestellt.

Viele Benutzeroberflächen hängen stark von Benutzerauthentifizierungsdaten ab. Bei dem Direktzugriffsendpunkt handelt es sich um eine Hilfs-API, die Benutzerinformationen verfügbar macht, ohne eine benutzerdefinierte Funktion implementieren zu müssen. Der Direktzugriffsendpunkt sorgt für zusätzlichen Komfort und ist auch nicht von den Kaltstartverzögerungen einer serverlosen Architektur betroffen.

## <a name="client-principal-data"></a>Clientprinzipaldaten

Durch das Clientprinzipaldatenobjekt werden benutzerbezogene Informationen für Ihre App verfügbar gemacht. Folgenden Eigenschaften sind im Clientprinzipalobjekt enthalten:

| Eigenschaft           | Beschreibung                                                                                                                                                                                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `identityProvider` | Der Name des [Identitätsanbieters](authentication-authorization.md).                                                                                                                                                                                                                                                                                              |
| `userId`           | Ein Azure Static Web Apps-spezifischer Bezeichner für den Benutzer. <ul><li>Der Wert ist App-spezifisch eindeutig. Für den gleichen Benutzer wird also beispielsweise in einer anderen Static Web Apps-Ressource ein anderer Wert vom Typ `userId` zurückgegeben.<li>Der Wert bleibt während der Lebensdauer eines Benutzers erhalten. Wenn Sie einen Benutzer löschen und wieder der App hinzufügen, wird ein neuer Wert vom Typ `userId` generiert.</ul> |
| `userDetails`      | Der Benutzername oder die E-Mail-Adresse des Benutzers. Von einigen Anbietern wird die [E-Mail-Adresse des Benutzers](authentication-authorization.md) zurückgegeben, von anderen das [Benutzerhandle](authentication-authorization.md).                                                                                                                                                                    |
| `userRoles`        | Ein Array der [zugewiesenen Rollen des Benutzers](authentication-authorization.md).                                                                                                                                                                                                                                                                                          |

Beim folgenden Beispiel handelt es sich um ein exemplarisches Clientprinzipalobjekt:

```json
{
  "identityProvider": "github",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "username",
  "userRoles": ["anonymous", "authenticated"]
}
```

## <a name="direct-access-endpoint"></a>Direktzugriffsendpunkt

Sie können eine Anforderung vom Typ `GET` an die Route `/.auth/me` senden und direkten Zugriff auf die Clientprinzipaldaten erhalten. Wenn der Zustand Ihrer Ansicht von Autorisierungsdaten abhängt, erzielen Sie mit diesem Ansatz die bestmögliche Leistung.

Für angemeldete Benutzer enthält die Antwort ein JSON-Clientprinzipalobjekt. Für Anforderungen von nicht authentifizierten Benutzern wird `null` zurückgegeben.

Mithilfe der [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup>-API können Sie unter Verwendung der folgenden Syntax auf die Clientprinzipaldaten zugreifen:

```javascript
async function getUserInfo() {
  const response = await fetch('/.auth/me');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API-Funktionen

Die API-Funktionen, die in Static Web Apps über das Azure Functions-Back-End verfügbar sind, haben Zugriff auf dieselben Benutzerinformationen wie eine Clientanwendung. Die API erhält zwar benutzerbezogene Informationen, führt aber keine eigenen Überprüfungen durch, ob der Benutzer authentifiziert ist oder ob sie einer erforderlichen Rolle entsprechen. Zugriffssteuerungsregeln werden in der Datei [`staticwebapp.config.json`](configuration.md#routes) definiert.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Clientprinzipaldaten werden im Anforderungsheader `x-ms-client-principal` an API-Funktionen übergeben. Die Clientprinzipaldaten werden als [Base64](https://www.wikipedia.org/wiki/Base64)-codierte Zeichenfolge gesendet, die ein serialisiertes JSON-Objekt enthält.

Die folgende Beispielfunktion zeigt, wie Benutzerinformationen gelesen und zurückgegeben werden:

```javascript
module.exports = async function (context, req) {
  const header = req.headers['x-ms-client-principal'];
  const encoded = Buffer.from(header, 'base64');
  const decoded = encoded.toString('ascii');

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded),
    },
  };
};
```

Mithilfe der [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup>-Browser-API können Sie unter Verwendung der folgenden Syntax auf die Antwort der API zugreifen. In diesem Beispiel wird davon ausgegangen, dass die obige Funktion `user` heißt:

```javascript
async function getUser() {
  const response = await fetch('/api/user');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

In einer C#-Funktion sind die Benutzerinformationen aus dem `x-ms-client-principal`-Header verfügbar, der in ein `ClaimsPrincipal`-Objekt oder Ihren eigenen benutzerdefinierten Typ deserialisiert werden kann. Der folgende Code zeigt, wie der Header in einen Zwischentyp, `ClientPrincipal`, entpackt wird, der dann in eine `ClaimsPrincipal`-Instanz umgewandelt wird.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Text;
using System.Text.Json;
using Microsoft.AspNetCore.Http;

public static class StaticWebAppsAuth
{
  private class ClientPrincipal
  {
      public string IdentityProvider { get; set; }
      public string UserId { get; set; }
      public string UserDetails { get; set; }
      public IEnumerable<string> UserRoles { get; set; }
  }

  public static ClaimsPrincipal Parse(HttpRequest req)
  {
      var principal = new ClientPrincipal();

      if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
      {
          var data = header[0];
          var decoded = Convert.FromBase64String(data);
          var json = Encoding.UTF8.GetString(decoded);
          principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
      }

      principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

      if (!principal.UserRoles?.Any() ?? true)
      {
          return new ClaimsPrincipal();
      }

      var identity = new ClaimsIdentity(principal.IdentityProvider);
      identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
      identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
      identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

      return new ClaimsPrincipal(identity);
  }
}
```

---

<sup>1</sup> Die [fetch](https://caniuse.com/#feat=fetch)-API und der Operator [await](https://caniuse.com/#feat=mdn-javascript_operators_await) werden in Internet Explorer nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von App-Einstellungen](application-settings.md)
