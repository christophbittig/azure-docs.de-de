---
title: 'Tutorial: Importieren und Veröffentlichen Ihrer ersten API in Azure API Management'
description: In diesem Tutorial importieren Sie eine OpenAPI-Spezifikations-API in Azure API Management und testen diese anschließend im Azure-Portal.
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: danlep
ms.openlocfilehash: 8ac58d354c5a92482f2cd47c59316fca9ba32025
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627100"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Tutorial: Importieren und Veröffentlichen Ihrer ersten API

In diesem Tutorial wird veranschaulicht, wie Sie eine Back-End-API mit OpenAPI-Spezifikation im JSON-Format in Azure API Management importieren. Microsoft stellt die in diesem Beispiel verwendete Back-End-API bereit und hostet sie in Azure unter [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Nachdem Sie die Back-End-API in API Management importiert haben, wird Ihre API Management-API zu einer Fassade für die Back-End-API. Sie können die Fassade an Ihre Anforderungen in API Management anpassen, ohne hierfür die Back-End-API bearbeiten zu müssen. Weitere Informationen finden Sie unter [Transformieren und Schützen Ihrer API](transform-api.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren einer API in API Management
> * Testen der API im Azure-Portal

Nach dem Import können Sie die API im Azure-Portal verwalten.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Neue API in API Management":::

## <a name="prerequisites"></a>Voraussetzungen

- Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
- [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Importieren und Veröffentlichen einer Back-End-API

In diesem Abschnitt wird gezeigt, wie Sie eine Back-End-API mit OpenAPI-Spezifikation importieren und veröffentlichen.

1. Wählen Sie im linken Navigationsbereich der API Management-Instanz **APIs** aus.
1. Wählen Sie die Kachel **OpenAPI** aus.
1. Wählen Sie im Fenster **Aus OpenAPI-Spezifikation erstellen** die Option **Vollständig** aus.
1. Geben Sie die Werte aus der folgenden Tabelle ein. Wählen Sie **Erstellen** aus, um die API zu erstellen.

   Sie können API-Werte während der Erstellung oder später über die Registerkarte **Einstellungen** festlegen.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Erstellen einer API":::


   |Einstellung|Wert|BESCHREIBUNG|
   |-------|-----|-----------|
   |**OpenAPI-Spezifikation**|*https:\//conferenceapi.azurewebsites.net?format=json*|Der Dienst, der die API implementiert. API Management leitet Anfragen an diese Adresse weiter. Der Dienst muss unter einer öffentlich zugänglichen Internetadresse gehostet werden. |
   |**Anzeigename**|Nachdem Sie die vorherige Dienst-URL eingegeben haben, füllt API Management dieses Feld basierend auf dem JSON-Code aus.|Der Name im [Entwicklerportal](api-management-howto-developer-portal.md) angezeigte Name.|
   |**Name**|Nachdem Sie die vorherige Dienst-URL eingegeben haben, füllt API Management dieses Feld basierend auf dem JSON-Code aus.|Ein eindeutiger Name für die API.|
   |**Beschreibung**|Nachdem Sie die vorherige Dienst-URL eingegeben haben, füllt API Management dieses Feld basierend auf dem JSON-Code aus.|Eine optionale Beschreibung der API.|
   |**URL-Schema**|**HTTPS**|Gibt an, welche Protokolle auf die API zugreifen können.|
   |**API-URL-Suffix**|*conference*|Das Suffix wird an die Basis-URL für den API Management-Dienst angehängt. API Management unterscheidet APIs anhand des Suffix. Daher muss jede API eines bestimmten Herausgebers ein eindeutiges Suffix haben.|
   |**Tags**| |Tags zum Organisieren von APIs für die Suche, Gruppierung oder Filterung.|
   |**Produkte**|**Unbegrenzt**|Zuordnung von einer oder mehreren APIs. Jede API Management-Instanz enthält zwei Beispielprodukte: **Starter** und **Unbegrenzt**. Zur Veröffentlichung einer API muss diese einem Produkt zugeordnet werden (in diesem Beispiel **Unbegrenzt**).<br/><br/> Sie können ein Produkt mit mehreren APIs versehen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Geben Sie den Produktnamen ein, oder wählen Sie ihn aus, um diese API einem anderen Produkt hinzuzufügen. Wiederholen Sie diesen Schritt, um die API mehreren Produkten hinzuzufügen. Sie können APIs Produkten auch später über die Seite **Einstellungen** hinzufügen.<br/><br/>  Weitere Informationen zu Produkten finden Sie unter [Erstellen und Veröffentlichen eines Produkts](api-management-howto-add-products.md).|
   |**Gateways**|**Verwaltet**|API-Gateways, die die API verfügbar machen. Dieses Feld ist nur bei Diensten mit **Developer**- und **Premium**-Tarif verfügbar.<br/><br/>**Verwaltet** gibt an, dass das Gateway in den API Management-Dienst integriert ist und von Microsoft in Azure gehostet wird. [Selbstgehostete Gateways](self-hosted-gateway-overview.md) sind nur bei Diensten mit Premium- und Developer-Tarif verfügbar. Sie können sie lokal oder in anderen Clouds bereitstellen.<br/><br/> Wenn keine Gateways ausgewählt sind, ist die API nicht verfügbar, und Ihre API-Anforderungen können nicht erfolgreich ausgeführt werden.|
   |**Versionsverwaltung für diese API?**|Auswählen bzw. Aufheben der Auswahl|Weitere Informationen finden Sie unter [Veröffentlichen mehrerer Versionen Ihrer API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Wenn Sie die API für API-Consumer veröffentlichen möchten, müssen Sie sie einem Produkt zuordnen.

2. Klicken Sie auf **Erstellen**.

Falls beim Importieren einer API-Definition Probleme auftreten, helfen Ihnen die Informationen in der [Liste mit den bekannten Problemen und Einschränkungen](api-management-api-import-restrictions.md) weiter.

## <a name="test-the-new-api-in-the-azure-portal"></a>Testen der neuen API im Azure-Portal

Sie können API-Vorgänge direkt über das Azure-Portal aufrufen. Dies ist ein einfacher Weg, die Vorgänge anzuzeigen und zu testen.

1. Wählen Sie im linken Navigationsbereich der API Management-Instanz **APIs** > **Demo Conference API** aus.
1. Wählen Sie die Registerkarte **Test** und dann **GetSpeakers** aus. Auf der Seite werden **Abfrageparameter** und **Header** angezeigt (falls zutreffend). **Ocp-Apim-Subscription-Key** wird automatisch für den Abonnementschlüssel eingefügt, der dieser API zugeordnet ist.
1. Wählen Sie **Senden** aus.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="API im Azure-Portal testen":::

   Das Back-End antwortet mit **200 OK** und einigen Daten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Importieren Ihrer ersten API
> * Testen der API im Azure-Portal

Im nächsten Tutorial erfahren Sie, wie Sie ein Produkt erstellen und veröffentlichen:

> [!div class="nextstepaction"]
> [Erstellen und Veröffentlichen eines Produkts](api-management-howto-add-products.md)
