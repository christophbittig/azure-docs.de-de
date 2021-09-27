---
title: Importieren einer Azure Web-App in Azure API Management | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Azure API Management verwenden, um eine Web-API zu importieren, die in Azure App Service gehostet wird.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/27/2021
ms.author: danlep
ms.openlocfilehash: 8d98a6c115b7f3d06b80a938c090bfa1b9c9794e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585088"
---
# <a name="import-an-azure-web-app-as-an-api"></a>Importieren einer Azure Web-App als API

In diesem Artikel wird gezeigt, wie Sie eine Azure Web-App in Azure API Management importieren und die importierte API mithilfe des Azure-Portals testen.

> [!NOTE]
> Sie können die API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs verwenden. Informationen zur Installation und zu den ersten Schritten finden Sie unter [Tutorial: Verwenden der API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs](visual-studio-code-tutorial.md).

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Eine in App Service gehostete Web-App importieren
> * Testen der API im Azure-Portal

## <a name="expose-web-app-with-api-management"></a>Verfügbarmachen der Web-App mit API Management

[Azure App Service](../app-service/overview.md) ist ein HTTP-basierter Dienst zum Hosten von Webanwendungen, REST-APIs und mobilen Back-Ends. API-Entwickler können ihre bevorzugten Technologiestapel und Pipelines verwenden, um APIs zu entwickeln, und ihre API-Back-Ends als Web-Apps in einer sicheren, skalierbaren Umgebung zu veröffentlichen. Verwenden Sie anschließend API Management, um die Web-Apps verfügbar zu machen, die APIs während ihres Lebenszyklus zu verwalten und zu schützen, und sie für Consumer zu veröffentlichen.

API Management wird aus mehreren Gründen als Umgebung empfohlen, um eine von einer Web-App gehostete API verfügbar zu machen:

* Entkoppeln der Verwaltung und Sicherung des Front-Ends, das API-Consumern zur Verfügung steht, von der Verwaltung und Überwachung der Back-End-Web-App
* Verwalten von Web-APIs, die als Web-Apps gehostet werden, in derselben Umgebung wie Ihre anderen APIs
* Anwenden von [Richtlinien](api-management-policies.md) zum Ändern des API-Verhaltens, z. B. Aufrufratenbegrenzung
* Verweisen von API-Consumern an das anpassbare [Entwicklerportal](api-management-howto-developer-portal.md) von API Management, um Ihre APIs zu entdecken und kennenzulernen, Zugriff anzufordern und auszuprobieren

Weitere Informationen finden Sie unter [Informationen zu API Management](api-management-key-concepts.md).

## <a name="openapi-specification-versus-wildcard-operations"></a>OpenAPI-Spezifikation im Vergleich zu Platzhaltervorgängen

API Management unterstützt den Import von Web-Apps, die in App Service gehostet werden, und eine OpenAPI-Spezifikation (Swagger-Definition) enthalten. Eine OpenAPI-Spezifikation ist jedoch nicht zwingend erforderlich.

* Wenn für die Web-App eine OpenAPI-Spezifikation in einer API-Definition konfiguriert ist, erstellt API Management API-Vorgänge, die direkt der Definition entsprechen, einschließlich erforderlicher Pfade, Parameter und Antworttypen. 

  Es wird empfohlen, eine OpenAPI-Spezifikation zu verwenden, da die API so mit hoher Genauigkeit in API Management importiert wird. Das gibt Ihnen die Flexibilität, Konfigurationen für jeden Vorgang separat zu überprüfen, zu verwalten, zu sichern und zu aktualisieren.

* Wenn keine OpenAPI-Spezifikation bereitgestellt wird, generiert API Management [Platzhaltervorgänge](add-api-manually.md#add-and-test-a-wildcard-operation) für die allgemeinen HTTP-Verben (GET, PUT etc.). Fügen Sie einen erforderlichen Pfad oder Parameter an einen Platzhaltervorgang an, um eine API-Anforderung an die Back-End-API zu übergeben.

  Bei Platzhaltervorgängen können Sie weiterhin die gleichen Features von API Management nutzen; Vorgänge werden jedoch standardmäßig nicht auf derselben Detailebene definiert. In beiden Fällen können Sie Vorgänge [bearbeiten](edit-api.md) oder der importierten API [hinzufügen](add-api-manually.md).
 
### <a name="example"></a>Beispiel
Ihre Back-End-Web-App unterstützt möglicherweise zwei GET-Vorgänge: 
*  `https://myappservice.azurewebsites.net/customer/{id}`
*  `https://myappservice.azurewebsites.net/customers`

Sie importieren die Web-App unter einem Pfad wie `https://contosoapi.azureapi.net/store` in Ihren API Management-Dienst. Die folgende Tabelle zeigt die Vorgänge, die in API Management importiert werden, entweder mit oder ohne OpenAPI-Spezifikation: 

| type |Importierte Vorgänge  |Beispielanforderungen |
|---------|---------|---------|
|OpenAPI-Spezifikation    | `GET  /customer/{id}`<br/><br/> `GET  /customers`         |  `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`       |
|Platzhalter     | `GET  /*`         | `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`  |

Der Platzhaltervorgang ermöglicht die gleichen Anforderungen an den Back-End-Dienst wie die Vorgänge in der OpenAPI-Spezifikation. Die von OpenAPI angegebenen Vorgänge können jedoch separat in API Management verwaltet werden. 

## <a name="prerequisites"></a>Voraussetzungen

+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Stellen Sie sicher, dass Ihr Abonnement einen API-Dienst enthält. Weitere Informationen finden Sie in der [App Service-Dokumentation](../app-service/index.yml).

  Schritte zum Erstellen einer Beispiel-Web-API und zum Veröffentlichen als Azure Web-App finden Sie unter:

    * [Tutorial: Erstellen einer Web-API mit ASP.NET Core](/aspnet/core/tutorials/first-web-api)
    * [Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio Code](/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importieren und Veröffentlichen einer Back-End-API

> [!TIP]
> Mit den folgenden Schritten starten Sie den Import mithilfe von Azure API Management im Azure-Portal. Sie können auch direkt in Ihrer Web-App eine Verknüpfung zu API Management erstellen, indem Sie im **API**-Menü der App **API Management** auswählen.  

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
1. Wählen Sie **App Service** aus der Liste aus.

    :::image type="content" source="media/import-app-service-as-api/app-service.png" alt-text="Erstellen aus App Service":::
1. Klicken Sie auf **Durchsuchen**, um die Liste der App Services in Ihrem Abonnement anzuzeigen.
1. Wählen Sie einen App Service. Wenn der ausgewählten Web-App eine OpenAPI-Definition zugeordnet ist, ruft API Management sie ab und importiert sie. 

    Wenn keine OpenAPI-Definition gefunden wird, macht API Management die API verfügbar, indem Platzhaltervorgänge für allgemeine HTTP-Verben generiert werden. 
1. Fügen Sie ein API-URL-Suffix hinzu. Das Suffix ist ein Name, der diese spezifische API in dieser API Management-Instanz identifiziert. Es muss in dieser APIM-Instanz eindeutig sein.
1. Veröffentlichen Sie die API, indem Sie sie einem Produkt zuordnen. In diesem Fall wird das Produkt „*Unlimited*“ verwendet. Wenn Sie möchten, dass die API veröffentlicht wird, und dann Entwicklern zur Verfügung steht, fügen Sie sie einem Produkt hinzu. Sie können dies während der Erstellung der API vornehmen oder später festlegen.

    > [!NOTE]
    > Bei Produkten handelt es sich um API-Zuordnungen. Sie können viele verschiedene APIs einfügen, und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Entwickler müssen ein Produkt zunächst abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die APIM-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.
    >
    > Standardmäßig enthält jede API Management-Instanz zwei Beispielprodukte:
    > * **Starter**
    > * **Unbegrenzt**   
1. Geben Sie weitere API-Einstellungen ein. Sie können die Werte während der Erstellung festlegen oder später über die Registerkarte **Einstellungen** konfigurieren. Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.
1. Klicken Sie auf **Erstellen**.
    :::image type="content" source="media/import-app-service-as-api/import-app-service.png" alt-text="Erstellen einer API aus App Service":::

## <a name="test-the-new-api-in-the-azure-portal"></a>Testen der neuen API im Azure-Portal

Vorgänge können direkt aus dem Azure-Portal aufgerufen werden. Dies ist ein einfacher Weg, die Vorgänge einer API anzuzeigen und zu testen. Sie können die API auch im [Entwicklerportal](api-management-howto-developer-portal.md) oder mit Ihren eigenen REST-Clienttools testen.

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
1. Wählen Sie die Registerkarte **Testen** aus.
1. Wählen Sie einen Vorgang aus.

    Die Seite zeigt Felder für Abfrageparameter und Felder für die Header. Einer der Header ist „Ocp-Apim-Subscription-Key“. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. Wenn Sie die API Management-Instanz erstellt haben, sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird. 
1. Klicken Sie auf **Senden**.

    Wenn die Anforderung erfolgreich ist, antwortet das Back-End mit **200 OK** und einigen Daten.

### <a name="test-wildcard-operation-in-the-portal"></a>Testen des Platzhaltervorgangs im Azure-Portal

Wenn Platzhaltervorgänge generiert werden, werden die Vorgänge möglicherweise nicht direkt der Back-End-API zugeordnet. Beispielsweise verwendet ein in API Management importierter GET-Platzhaltervorgang standardmäßig den Pfad `/`. Ihre Back-End-API unterstützt jedoch möglicherweise einen GET-Vorgang unter folgendem Pfad:

`/api/TodoItems`

Sie können den Pfad `/api/TodoItems` wie folgt testen. 

1. Wählen Sie die erstellte API und den Vorgang aus.
1. Wählen Sie die Registerkarte **Testen** aus.
1. Aktualisieren Sie unter **Vorlagenparameter** den Wert neben dem Platzhalternamen (*). Geben Sie beispielsweise `api/TodoItems` ein. Dieser Wert wird an den Pfad `/` für den Platzhaltervorgang angefügt.

    :::image type="content" source="media/import-app-service-as-api/test-wildcard-operation.png" alt-text="Testen des Platzhaltervorgangs":::
1. Wählen Sie **Send** (Senden) aus.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
