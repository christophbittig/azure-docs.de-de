---
title: Anpassen eines HTTP-Endpunkts in Azure Functions
description: Hier erfahren Sie, wie Sie einen HTTP-Trigger-Endpunkt in Azure Functions anpassen.
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 856845022e63a96b9e5d56d1e869dec55a2511be
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892170"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Anpassen eines HTTP-Endpunkts in Azure Functions

In diesem Artikel erfahren Sie, wie Sie mit Azure Functions höchst flexibel skalierbare APIs erstellen können. Zu Azure Functions gehört eine Sammlung von integrierten HTTP-Triggern und -Bindungen, die Ihnen das Erstellen eines Endpunkts in einer Vielzahl von Sprachen wie z.B. Node.js und C# erleichtern. In diesem Artikel passen Sie einen HTTP-Trigger so an, dass er bestimmte Aktionen in Ihrem API-Entwurf verarbeitet. Sie bereiten sich auch auf eine Erweiterung Ihrer API vor, indem Sie sie in Azure Functions-Proxys integrieren und Modell-APIs einrichten. Diese Aufgaben erfolgen auf der Grundlage der serverlosen Computeumgebung für Functions. Daher müssen Sie sich keine Gedanken über die Skalierung der Ressourcen machen – Sie können sich ganz auf Ihre API-Logik konzentrieren.

## <a name="prerequisites"></a>Voraussetzungen 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Die resultierende Funktion wird für den restlichen Teil dieses Artikels verwendet.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="customize-your-http-function"></a>Anpassen der HTTP-Funktion

Standardmäßig ist Ihre HTTP-Triggerfunktion so konfiguriert, dass sie alle HTTP-Methoden akzeptiert. Sie können auch die Standard-URL (`http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`) verwenden. In diesem Abschnitt ändern Sie die Funktion, sodass sie nur auf GET-Anforderungen mit `/api/hello` antwortet. 

1. Navigieren Sie im Azure-Portal zu Ihrer Funktion. Wählen Sie im Menü auf der linken Seite **Integration** aus, und wählen Sie dann **HTTP (req)** unter **Trigger** aus.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Anpassen einer HTTP-Funktion":::

1. Verwenden Sie die HTTP-Triggereinstellungen, wie in der folgenden Tabelle angegeben.

    | Feld | Beispielwert | BESCHREIBUNG |
    |---|---|---|
    | Routenvorlage | hello | Bestimmt, welche Route verwendet wird, um diese Funktion aufzurufen |
    | Autorisierungsstufe | Anonym | Optional: Ermöglicht den Zugriff auf Ihre Funktion ohne einen API-Schlüssel |
    | Ausgewählte HTTP-Methoden | GET | Bestimmt, dass nur ausgewählte HTTP-Methoden zum Aufrufen dieser Funktion verwendet werden können |

    Sie haben das Basispfadpräfix `/api` nicht in die Routenvorlage aufgenommen, weil es durch eine globale Einstellung festgelegt wird.

1. Wählen Sie **Speichern** aus.

Weitere Informationen zum Anpassen von HTTP-Funktionen finden Sie unter [HTTP- und Webhookbindungen in Azure Functions](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>Testen der API

Als Nächstes testen Sie die Funktion, um zu sehen, wie sie mit der neuen API-Oberfläche funktioniert:
1. Wählen Sie auf der Funktionsseite im linken Menü **Code + Test** aus.

1. Wählen Sie aus dem oberen Menü **Funktions-URL abrufen** aus, und kopieren Sie die URL. Vergewissern Sie sich, dass jetzt der Pfad `/api/hello` verwendet wird.
 
1. Kopieren Sie die URL in eine neue Browserregisterkarte oder in Ihren bevorzugten REST-Client. 

   Browser verwenden standardmäßig GET.
 
1. Fügen Sie der Abfragezeichenfolge in der URL Parameter hinzu. 

   Beispiel: `/api/hello/?name=John`.
 
1. Drücken Sie die EINGABETASTE, um sicherzustellen, dass es funktioniert. Daraufhin sollte die Antwort „*Hello John*“ angezeigt werden.

1. Sie können auch versuchen, den Endpunkt mit einer anderen HTTP-Methode aufzurufen, um sicherzustellen, dass die Funktion nicht ausgeführt wird. Hierzu müssen Sie einen REST-Client wie cURL, Postman oder Fiddler verwenden.

## <a name="proxies-overview"></a>Übersicht über Proxy

Im nächsten Abschnitt zeigen Sie Ihre API über einen Proxy an. Mit Azure-Funktionsproxys können Sie Anforderungen an andere Ressourcen weiterleiten. Sie definieren einen HTTP-Endpunkt wie für HTTP-Trigger. Anstatt aber Code zu schreiben, der ausgeführt wird, wenn dieser Endpunkt aufgerufen wird, geben Sie eine URL für eine Remoteimplementierung an. Dadurch können Sie mehrere API-Quellen in einer einzelnen API-Oberfläche zusammenstellen, die Clients einfach nutzen können. Dies ist nützlich, wenn Sie Ihre API als Microservices erstellen möchten.

Ein Proxy kann auf HTTP-Ressourcen wie die folgenden verweisen:
- Azure-Funktionen 
- API-Apps in [Azure App Service](../app-service/overview.md)
- Docker-Container in [App Service unter Linux](../app-service/overview.md#app-service-on-linux)
- Andere gehostete APIs

Weitere Informationen zu Proxys finden Sie unter [Arbeiten mit Proxys in Azure Functions (Vorschau)].

## <a name="create-your-first-proxy"></a>Erstellen Ihres ersten Proxys

In diesem Abschnitt erstellen Sie einen neuen Proxy, der als Front-End für Ihre übergeordnete API dient. 

### <a name="setting-up-the-frontend-environment"></a>Einrichten der Front-End-Umgebung

Wiederholen Sie die Schritte zum [Erstellen einer Funktionen-App](./functions-get-started.md), um eine neue Funktionen-App zu erstellen, in der Sie den Proxy erstellen. Die URL dieser neuen App dient als Front-End für unsere API, und die Funktions-App, die Sie zuvor bearbeitet haben, dient als Back-End.

1. Navigieren Sie zu Ihrer neuen Front-End-Funktionen-App im Portal.
1. Wählen Sie **Konfiguration** und anschließend **Anwendungseinstellungen** aus.
1. Scrollen Sie nach unten zu **Anwendungseinstellungen**, wo Schlüssel/Wert-Paare gespeichert sind, und erstellen Sie eine neue Einstellung mit dem Schlüssel `HELLO_HOST`. Legen Sie deren Wert auf den Host Ihrer Back-End-Funktionen-App fest, z.B. `<YourBackendApp>.azurewebsites.net`. Dieser Wert ist Teil der URL, die Sie zuvor kopiert haben, als Sie die HTTP-Funktion getestet haben. Sie geben diese Einstellung später in der Konfiguration an.

    > [!NOTE] 
    > App-Einstellungen werden für die Hostkonfiguration empfohlen, um eine hartcodierte Umgebungsabhängigkeit für den Proxy zu verhindern. Die Verwendung dieser App-Einstellungen bedeutet, dass Sie die Proxykonfiguration zwischen Umgebungen verschieben können, und die umgebungsspezifischen App-Einstellungen werden angewendet.

1. Wählen Sie **Speichern** aus.

### <a name="creating-a-proxy-on-the-frontend"></a>Erstellen eines Proxys auf dem Front-End

1. Navigieren Sie zurück zu Ihrer Front-End-Funktions-App im Portal.

1. Wählen Sie im linken Menü die Option **Proxys** und dann **Hinzufügen** aus. 

1. Verwenden Sie auf der Seite **neuer Proxy** die Einstellungen in der folgenden Tabelle, und wählen Sie dann **Erstellen** aus.

    | Feld | Beispielwert | BESCHREIBUNG |
    |---|---|---|
    | Name | HelloProxy | Ein Anzeigename für die Verwaltung |
    | Routenvorlage | /api/remotehello | Bestimmt, welche Route verwendet wird, um diesen Proxy aufzurufen |
    | Back-End-URL | https://%HELLO_HOST%/api/hello | Gibt den Endpunkt an, der für die Anforderung als Proxy dienen soll |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Erstellen eines Proxys":::

    Beachten Sie, dass Azure Functions-Proxys das Basispfadpräfix `/api` nicht bereitstellen. Es muss in der Routenvorlage enthalten sein. Die `%HELLO_HOST%`-Syntax verweist auf die App-Einstellung, die Sie zuvor erstellt haben. Die aufgelöste URL verweist auf die ursprüngliche Funktion.

1. Probieren Sie Ihren neuen Proxy aus, indem Sie die Proxy-URL kopieren und im Browser oder mit Ihrem bevorzugten HTTP-Client testen:
    - Für eine anonyme Funktion verwenden Sie `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`.
    - Für eine Funktion mit Autorisierung verwenden Sie `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`.

## <a name="create-a-mock-api"></a>Erstellen einer Modell-API

Als Nächstes verwenden Sie einen Proxy, um eine Modell-API für die Lösung zu erstellen. Durch diesen Proxy kann die Cliententwicklung fortgesetzt werden, ohne das Back-End vollständig zu implementieren. Sie können später bei der Entwicklung eine neue Funktions-App erstellen, die diese Logik unterstützt, und den Proxy entsprechend umleiten.

Um diese Modell-API zu erstellen, erstellen Sie einen neuen Proxy. Dieses Mal verwenden Sie den [App Service-Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Navigieren Sie zunächst zu Ihrer Funktionen-App im Portal. Wählen Sie **Plattformfeatures** aus, und suchen Sie unter **Entwicklungstools** nach **App Service-Editor**. Der App Service-Editor wird auf einer neuen Registerkarte geöffnet.

Wählen Sie im linken Navigationsbereich `proxies.json` aus. In dieser Datei wird die Konfiguration für alle Ihre Proxys gespeichert. Wenn Sie eine der [Functions-Bereitstellungsmethoden](./functions-continuous-deployment.md) verwenden, verwalten Sie diese Datei in der Quellcodeverwaltung. Weitere Informationen zu dieser Datei finden Sie unter [Erweiterte Konfiguration für Proxys](./functions-proxies.md#advanced-configuration).

Wenn Sie die bisherigen Schritte nachvollzogen haben, sollte Ihre Datei „proxies.json“ wie folgt aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Als Nächstes fügen Sie Ihre Modell-API hinzu. Ersetzen Sie die Datei „proxies.json“ durch den folgenden Code:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Mit diesem Code wird ein neuer Proxy (`GetUserByName`) ohne die `backendUri`-Eigenschaft hinzugefügt. Statt eine andere Ressource aufzurufen, wird die Standardantwort von Proxys geändert, indem die Antwort überschrieben wird. Das Überschreiben von Anforderungen und Antworten kann auch in Verbindung mit einer Back-End-URL genutzt werden. Diese Technik ist besonders nützlich, wenn Proxyfunktionen in einem Legacysystem verwendet werden, wo Sie möglicherweise Header, Abfrageparameter usw. ändern müssen. Weitere Informationen zum Überschreiben von Anforderungen und Antworten finden Sie unter [Ändern von Anforderungen und Antworten in Proxys](./functions-proxies.md).

Testen Sie Ihre Modell-API durch Aufrufen des Endpunkts `<YourProxyApp>.azurewebsites.net/api/users/{username}` mit einem Browser oder Ihrem bevorzugten REST-Client. Sie müssen _{username}_ mit einem Zeichenfolgenwert eines Benutzernamens ersetzen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie eine API für Azure Functions erstellt und angepasst wird. Außerdem haben Sie gelernt, wie Sie mehrere APIs (darunter Modelle) zu einer einheitlichen API-Oberfläche verknüpften können. Sie können diese Techniken verwenden, um APIs von beliebiger Komplexität zu erstellen, während Sie das von Azure Functions bereitgestellte serverlose Computemodell nutzen.

Die folgenden Referenzen können bei der weiteren Entwicklung Ihrer API hilfreich sein:

- [HTTP- und Webhookbindungen in Azure Functions](./functions-bindings-http-webhook.md)
- [Arbeiten mit Proxys in Azure Functions (Vorschau)]
- [Dokumentieren einer Azure Functions-API (Vorschau)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Arbeiten mit Proxys in Azure Functions (Vorschau)]: ./functions-proxies.md
