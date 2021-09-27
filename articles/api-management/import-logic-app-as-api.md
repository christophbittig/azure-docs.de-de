---
title: Importieren einer Logik-App als eine API mit dem Azure-Portal | Microsoft-Dokumentation
description: Dieser Artikel veranschaulicht, wie Sie API Management (APIM) verwenden, um Logik-Apps als API zu importieren.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: danlep
ms.openlocfilehash: 509a143a368c4626b7d0a39e1c3f2d15c8a3d0b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667766"
---
# <a name="import-a-logic-app-as-an-api"></a>Importieren einer Logik-App als API

Dieser Artikel zeigt, wie Sie eine Logik-App als eine API importieren und die importierte API testen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> -   Importieren einer Logik-App als API
> -   Testen der API im Azure-Portal

## <a name="prerequisites"></a>Voraussetzungen

-   Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
-   Stellen Sie sicher, dass Ihr Abonnement eine Logik-App enthält, die einen HTTP-Endpunkt verfügbar macht. Weitere Informationen finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Logik-Apps](../logic-apps/logic-apps-http-endpoint.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importieren und Veröffentlichen einer Back-End-API

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
1. Wählen Sie **Logik-App** in der Liste **Neue API hinzufügen** aus.

    :::image type="content" source="./media/import-logic-app-as-api/logic-app-select.png" alt-text="Auswählen der Logik-App-Kategorie":::

1. Klicken Sie auf **Durchsuchen**, um die Liste der Logik-Apps mit HTTP-Trigger in Ihrem Abonnement anzuzeigen. 
    * Logik-Apps *ohne* HTTP-Trigger sind nicht in der Liste aufgeführt.

    :::image type="content" source="./media/import-logic-app-as-api/browse-logic-apps.png" alt-text="Suchen nach vorhandenen Logik-Apps mit dem richtigen Trigger":::

1. Wählen Sie die Logik-App aus. 

    :::image type="content" source="./media/import-logic-app-as-api/select-logic-app-import-2.png" alt-text="Auswählen der Logik-App":::

1. API Management sucht den Swagger, der der ausgewählten App zugeordnet ist, ruft ihn ab und importiert ihn.
1. Fügen Sie ein API-URL-Suffix hinzu. 
    * Das Suffix identifiziert eindeutig diese spezifische API in dieser API Management-Instanz.

    :::image type="content" source="./media/import-logic-app-as-api/create-from-logic-app.png" alt-text="Ausfüllen der Felder":::

1. Wenn die API veröffentlicht werden und für Entwickler verfügbar sein soll, wechseln Sie in den **Vollbildmodus**, und ordnen Sie sie einem **Produkt** zu. In diesem Beispiel wird das Produkt *Unlimited* verwendet. 
    * Sie können Ihre API einem Produkt entweder während der Erstellung oder später über die Registerkarte **Einstellungen** hinzufügen.

    >[!NOTE]
    > Produkte sind Zuordnungen einer oder mehrerer APIs, die Entwicklern über das Entwicklerportal angeboten werden. Entwickler müssen zunächst ein Produkt abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonniert haben, erhalten sie einen Abonnementschlüssel für jede API in diesem Produkt. Als Ersteller der API Management-Instanz sind Sie Administrator und haben dadurch standardmäßig alle Produkte abonniert.
    >
    > Jede API Management-Instanz enthält zwei Standardbeispielprodukte:
    > - **Starter**
    > - **Unbegrenzt**

1. Geben Sie weitere API-Einstellungen ein. 
    * Sie können diese Werte während der Erstellung oder später über die Registerkarte **Einstellungen** festlegen. Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.
1. Klicken Sie auf **Erstellen**.

## <a name="test-the-api-in-the-azure-portal"></a>Testen der API im Azure-Portal

Vorgänge können direkt aus dem Azure-Portal aufgerufen werden. Dies ist ein einfacher Weg, die Vorgänge einer API anzuzeigen und zu testen.

:::image type="content" source="./media/import-logic-app-as-api/test-logic-app-api.png" alt-text="Testen der Logik-App":::

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
2. Wählen Sie die Registerkarte **Testen** aus.
3. Wählen Sie den Vorgang aus, den Sie testen möchten.

    * Auf der Seite werden Felder für Abfrageparameter und Header angezeigt. 
    * Einer der Header ist „Ocp-Apim-Subscription-Key“. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. 
    * Als Ersteller der API Management-Instanz sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird.

4. Klicken Sie auf **Senden**.

    * Wenn der Test erfolgreich ist, antwortet das Back-End mit **200 OK** und einigen Daten.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Jede Logik-App enthält den Vorgang **manual-invoke**. Um Ihre API aus mehreren Logik-Apps zusammenzusetzen und Konflikte zu vermeiden, müssen Sie die Funktion umbenennen.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
