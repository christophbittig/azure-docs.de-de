---
title: 'Tutorial: Simulieren von API-Antworten in API Management – Azure-Portal | Microsoft-Dokumentation'
description: In diesem Tutorial verwenden Sie API Management, um eine Richtlinie für eine API festzulegen, damit eine simulierte Antwort zurückgegeben wird, wenn das Back-End, das echte Antworten sendet, nicht verfügbar ist.
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: afccd6da3374147c77a2caf7bcc9b352b078c11c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646490"
---
# <a name="tutorial-mock-api-responses"></a>Tutorial: Simulieren von API-Antworten

Back-End-APIs können in eine APIM-API (API Management) importiert oder manuell erstellt und verwaltet werden. Die Schritte in diesem Tutorial veranschaulichen das Verwenden von APIM zum Erstellen einer leeren API, die manuell verwaltet wird, sowie das Festlegen einer Richtlinie für eine API, sodass diese eine simulierte Antwort zurückgibt. Diese Methode ermöglicht Entwicklern das Fortsetzen von Implementierung und Tests der APIM-Instanz, selbst wenn das Back-End nicht verfügbar ist und keine echten Antworten senden kann. 

Die Möglichkeit zum Simulieren von Antworten kann in verschiedenen Szenarien nützlich sein:

+ Die API-Facade wird entworfen, bevor das Back-End implementiert wird. Oder das Back-End wird parallel entwickelt.
+ Das Back-End ist vorübergehend nicht betriebsbereit, oder es kann nicht skaliert werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Test-API 
> * Hinzufügen eines Vorgangs zur Test-API
> * Aktivieren der Antwortsimulation
> * Testen der simulierten API


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Simulierte API-Antwort":::

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Machen Sie sich mit dem [Konzept von Richtlinien in Azure API Management](api-management-howto-policies.md) vertraut.
+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

## <a name="create-a-test-api"></a>Erstellen einer Test-API 

In diesem Abschnitt erfahren Sie, wie Sie eine leere API ohne Back-End erstellen. 


1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrer API Management-Instanz.
1. Wählen Sie **APIs** >  **+ API hinzufügen** > **Leere API** aus.
1. Wählen Sie im Fenster **Leere API erstellen** die Option **Vollständig** aus.
1. Geben Sie *Test API* unter **Anzeigename** ein.
1. Wählen Sie **Unbegrenzt** für **Produkte** ein.
1. Stellen Sie sicher, dass **Verwaltet** in **Gateways** ausgewählt ist.
1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Erstellen einer leeren API":::

## <a name="add-an-operation-to-the-test-api"></a>Hinzufügen eines Vorgangs zur Test-API

Eine API macht mindestens einen Vorgang verfügbar. In diesem Abschnitt fügen Sie der von Ihnen erstellten leeren API einen Vorgang hinzu. Das Aufrufen des Vorgangs nach Abschluss der Schritte in diesem Abschnitt führt zu einem Fehler. Sie erhalten keine Fehler mehr, sobald Sie später die Schritte im Abschnitt [Aktivieren der Antwortsimulation](#enable-response-mocking) ausführen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
1. Klicken Sie auf **+ Vorgang hinzufügen**.
1. Geben Sie im Fenster **Front-End** die folgenden Werte ein.

     | Einstellung             | Wert                             | BESCHREIBUNG                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Anzeigename**    | *Test call*                       | Der im [Entwicklerportal](api-management-howto-developer-portal.md) angezeigte Name.                                                                                                                                       |
    | **URL** (HTTP-Verb) | GET                               | Wählen Sie eines der vordefinierten HTTP-Verben aus.                                                                                                                                         |
    | **URL**             | */test*                           | Ein URL-Pfad für die API.                                                                                                                                                                       |
    | **Beschreibung**     |                                   |  Optionale Beschreibung des Vorgangs, die als Dokumentation im Entwicklerportal für Entwickler dient, die diese API verwenden.                                                    |
    
1. Wählen Sie die Registerkarte **Antworten** unter den Feldern „URL“, „Anzeigename“ und „Beschreibung“ aus. Geben Sie auf dieser Registerkarte Einstellungen ein, die Statuscodes, Inhaltstypen, Beispiele und Schemas für die Antwort definieren.
1. Wählen Sie **+ Antwort hinzufügen** und dann aus der Liste **200 OK** aus.
1. Wählen Sie unter der Überschrift **Darstellungen** auf der rechten Seite **+ Darstellung hinzufügen** aus.
1. Geben Sie *application/json* in das Suchfeld ein, und wählen Sie den Inhaltstyp **application/json** aus.
1. Geben Sie im Testfeld **Beispiel**`{ "sampleField" : "test" }` ein.
1. Wählen Sie **Speichern** aus.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="API-Vorgang hinzufügen" border="false":::

Auch wenn es für dieses Beispiel nicht erforderlich ist, können Sie weitere Einstellungen für einen API-Vorgang auf anderen Registerkarten wie den folgenden konfigurieren:


|Registerkarte      |BESCHREIBUNG  |
|---------|---------|
|**Abfrage**     |  Fügen Sie Abfrageparameter hinzu. Neben einem Namen und einer Beschreibung können Sie Werte angeben, die einem Abfrageparameter zugewiesen werden. Einer der Werte kann als Standard markiert werden (optional).        |
|**Anforderung**     |  Definieren Sie Inhaltstypen, Beispiele und Schemas für die Anforderung.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

So beginnen Sie mit der Verwendung der Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Führen Sie den Befehl [az apim api operation create](/cli/azure/apim/api/operation#az_apim_api_operation_create) aus, um der Test-API einen Vorgang hinzuzufügen.

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Führen Sie den Befehl [az apim api operation list](/cli/azure/apim/api/operation#az_apim_api_operation_list) aus, um alle Vorgänge für eine API anzuzeigen:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Führen Sie zum Entfernen eines Vorgangs den Befehl [az apim api operation delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) aus. Rufen Sie die Vorgangs-ID über den vorherigen Befehl ab.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Verwenden Sie diesen Vorgang auch im restlichen Artikel.

---

## <a name="enable-response-mocking"></a>Aktivieren der Antwortsimulation

1. Wählen Sie die API aus, die Sie im Schritt [Erstellen einer Test-API](#create-a-test-api) erstellt haben.
1. Wählen Sie den Testvorgang aus, den Sie hinzugefügt haben.
1. Vergewissern Sie sich im Fenster rechts, dass die Registerkarte **Entwurf** ausgewählt ist.
1. Wählen Sie im Fenster **Eingehende Verarbeitung** die Option **+ Richtlinie hinzufügen** aus.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Verarbeitungsrichtlinie hinzufügen" border="false":::

1. Wählen Sie im Katalog **Mock responses** (Modellantworten) aus.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Kachel mit der Richtlinie für Modellantworten" border="false":::

1. Geben Sie im Textfeld **API Management response** (API Management-Antwort) die Zeichenfolge **200 OK, application/json** ein. Diese Auswahl gibt an, dass Ihre API das Antwortbeispiel zurückgeben soll, das Sie im vorherigen Abschnitt definiert haben.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Modellantwort festlegen":::

1. Wählen Sie **Speichern** aus.

    > [!TIP]
    > Ein gelber Balken mit dem Text **Die Antwortsimulation ist aktiviert** für die API gibt an, dass die von API Management zurückgegebenen Antworten von der [Simulationsrichtlinie](api-management-advanced-policies.md#mock-response) simuliert und nicht vom Back-End generiert werden.

## <a name="test-the-mocked-api"></a>Testen der simulierten API

1. Wählen Sie die API aus, die Sie im Schritt [Erstellen einer Test-API](#create-a-test-api) erstellt haben.
1. Wählen Sie die Registerkarte **Testen** aus.
1. Vergewissern Sie sich, dass die **Testaufruf**-API ausgewählt ist. Wählen Sie **Senden** aus, um einen Testaufruf durchzuführen.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testen der simulierten API":::

1. In **HTTP-Antwort** wird der JSON-Code angezeigt, der als Beispiel im ersten Abschnitt des Tutorials angegeben wurde.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="HTTP-Modellantwort":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Test-API
> * Hinzufügen eines Vorgangs zur Test-API
> * Aktivieren der Antwortsimulation
> * Testen der simulierten API

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
