---
title: Importieren einer SOAP-API und Konvertieren dieser in REST mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine SOAP-API importieren, sie mit API Management in eine REST-API umwandeln und die API dann im Azure- und im Entwicklerportal testen.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: danlep
ms.openlocfilehash: 711eb5ed6e92a50ba43565b393c25f270566a36b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047416"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>Importieren einer SOAP-API und Konvertieren dieser in REST

Dieser Artikel zeigt, wie Sie eine SOAP-API importieren und in REST konvertieren. Der Artikel zeigt außerdem, wie Sie die APIM-API testen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Importieren einer SOAP-API und Konvertieren dieser in REST
> * Testen der API im Azure-Portal
> * Testen der API im Entwicklerportal

## <a name="prerequisites"></a>Voraussetzungen

Absolvieren Sie den folgende Schnellstart: [Erstellen einer Azure API Management-Instanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importieren und Veröffentlichen einer Back-End-API

1. Wählen Sie unter **API MANAGEMENT** die Option **APIs** aus.
2. Wählen Sie **WSDL** in der Liste **Neue API hinzufügen** aus.

    ![SOAP-API](./media/restify-soap-api/wsdl-api.png)
3. Geben Sie in **WSDL-Spezifikation** die URL Ihrer SOAP-API ein.
4. Klicken Sie auf das Optionsfeld **SOAP nach REST**. Wenn auf diese Option geklickt wird, versucht APIM, eine automatische Transformation zwischen XML und JSON durchzuführen. In diesem Fall sollten Consumer die API als RESTful-API aufrufen, die JSON zurückgibt. APIM konvertiert sämtliche Anforderungen in SOAP-Aufrufe.

    ![SOAP nach REST](./media/restify-soap-api/soap-to-rest.png)

5. Drücken Sie auf die Registerkarte.

    Die folgenden Felder werden mit den Informationen aus der SOAP-API aufgefüllt: Anzeigename, Name, Beschreibung.
6. Fügen Sie ein API-URL-Suffix hinzu. Das Suffix ist ein Name, der diese spezifische API in dieser APIM-Instanz identifiziert. Es muss in dieser APIM-Instanz eindeutig sein.
9. Veröffentlichen Sie die API, indem Sie sie einem Produkt zuordnen. In diesem Fall wird das Produkt „*Unlimited*“ verwendet.  Wenn Sie möchten, dass die API veröffentlicht wird und dann Entwicklern zur Verfügung steht, fügen Sie sie einem Produkt hinzu. Sie können dies während der Erstellung der API vornehmen oder später festlegen.

    Bei Produkten handelt es sich um API-Zuordnungen. Sie können eine Reihe von APIs einfügen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Entwickler müssen ein Produkt zunächst abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonnieren, erhalten sie einen Abonnementschlüssel, der für jede API in diesem Produkt gilt. Wenn Sie die APIM-Instanz erstellt haben, sind Sie bereits Administrator und haben dadurch standardmäßig alle Produkte abonniert.

    Standardmäßig enthält jede API Management-Instanz zwei Beispielprodukte:

    * **Starter**
    * **Unbegrenzt**   
10. Klicken Sie auf **Erstellen**.

## <a name="test-the-new-api-in-the-azure-portal"></a>Testen der neuen API im Azure-Portal

Vorgänge können direkt aus dem Azure-Portal aufgerufen werden. Dies ist ein einfacher Weg, die Vorgänge einer API anzuzeigen und zu testen.  

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
2. Wählen Sie die Registerkarte **Testen** aus.
3. Wählen Sie einen Vorgang aus.

    Die Seite zeigt Felder für Abfrageparameter und Felder für die Header. Einer der Header ist „Ocp-Apim-Subscription-Key“. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. Wenn Sie die APIM-Instanz erstellt haben, sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird. 
1. Klicken Sie auf **Senden**.

    Das Back-End antwortet mit **200 OK** und einigen Daten.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)