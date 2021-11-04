---
title: Sichern einer Einzelseiten-Webanwendung mit nicht interaktiver Anmeldung in Microsoft Azure Maps
titleSuffix: Azure Maps
description: Konfigurieren einer Einzelseiten-Webanwendung mit nicht interaktiver rollenbasierter Zugriffssteuerung von Azure (Azure RBAC) und Azure Maps Web SDK.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js, subject-rbac-steps
ms.openlocfilehash: 8f3249cec073e7a1fd6fdabd6a7af24ce5a2c2d2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443583"
---
# <a name="how-to-secure-a-single-page-web-application-with-non-interactive-sign-in"></a>Schützen einer Einzelseiten-Webanwendung mit nicht interaktiver Anmeldung

In diesem Artikel wird beschrieben, wie Sie eine Einzelseiten-Webanwendung mit Azure Active Directory (Azure AD) schützen, wenn sich der Benutzer nicht bei Azure AD anmelden kann.

Um diesen nicht-interaktiven Authentifizierungsablauf zu bilden, erstellen wir einen sicheren Azure Function-Webdienst, der für den Erwerb von Zugriffstoken von Azure AD verantwortlich ist. Dieser Webdienst ist nur für Ihre Einzelseiten-Webanwendung verfügbar.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps kann Zugriffstoken aus Benutzeranmeldungs- oder interaktiven Flows unterstützen. Interaktive Flows ermöglichen einen eingeschränkteren Bereich der Zugriffssperrung und Geheimhaltungsverwaltung.

## <a name="create-an-azure-function"></a>Erstellen einer Azure-Funktion

Erstellen einer gesicherten Webdienstanwendung, die für die Authentifizierung bei Azure AD zuständig ist:

1. Erstellen Sie eine Funktion im Azure-Portal. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Functions](../azure-functions/functions-get-started.md).

2. Konfigurieren Sie die CORS-Richtlinie für die Azure-Funktion so, dass die Einzelseiten-Webanwendung darauf zugreifen kann. Die CORS-Richtlinie gewährleistet, dass Browser-Clients nur die zulässigen Ursprünge Ihrer Webanwendung verwenden. Weitere Informationen finden Sie unter [Hinzufügen der CORS-Funktion](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Fügen Sie der Azure-Funktion eine systemseitig zugewiesene Identität hinzu](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity), um das Erstellen eines Dienstprinzipals für die Authentifizierung bei Azure AD zu ermöglichen.  

4. Gewähren Sie der systemseitig zugewiesenen Identität rollenbasierten Zugriff auf das Azure Maps-Konto. Details hierzu finden Sie unter [Gewähren von rollenbasiertem Zugriff](#grant-role-based-access-for-users-to-azure-maps).

5. Schreiben Sie Code für die Azure-Funktion, mit dem sie Azure Maps-Zugriffstoken mittels einer systemseitig zugewiesenen Identität mit einem der unterstützten Mechanismen oder dem REST-Protokoll abruft. Weitere Informationen finden Sie unter [Erhalten von Token für Azure-Ressourcen](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    Hier ein Beispiel für ein REST-Protokoll:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Und hier eine Beispielantwort:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Konfigurieren Sie die Sicherheit für den HttpTrigger der Azure-Funktion:

   1. [Erstellen eines Funktionszugriffsschlüssels](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   1. [Sichern Sie den HTTP-Endpunkt](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) für die in Produktion befindliche Azure-Funktion.

7. Konfigurieren Sie eine Azure Maps Web SDK Webanwendung.

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://{App-Name}.azurewebsites.net/api/{Function-Name}?code={API-Key}';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nächste Schritte

Weiteres Verstehen eines Szenarios einer Einzelseiten-Webanwendung:
> [!div class="nextstepaction"]
> [Einzelseitenanwendung](../active-directory/develop/scenario-spa-overview.md)

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Erkunden Sie weitere Beispiele für die Integration von Azure AD in Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
