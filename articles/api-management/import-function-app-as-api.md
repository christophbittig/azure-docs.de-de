---
title: Importieren einer Azure-Funktions-App als API in API Management
titleSuffix: Azure API Management
description: Dieser Artikel veranschaulicht, wie Sie eine Azure-Funktions-App als API in Azure API Management importieren.
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
ms.openlocfilehash: f4a90656164931a6d625fb486737eb970c08e89a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667804"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importieren einer Azure-Funktionen-App als API in Azure API Management

Azure API Management unterstützt das Importieren von Azure-Funktionen-Apps als neue APIs und das Anfügen dieser Apps an vorhandene APIs. Bei diesem Prozess wird automatisch ein Hostschlüssel in der Azure-Funktions-App generiert, der dann einem benannten Wert in Azure API Management zugewiesen wird.

In diesem Artikel werden die Schritte zum Importieren und Testen einer Azure-Funktions-App als API in Azure API Management erläutert. 

Sie lernen Folgendes:

> [!div class="checklist"]
> * Importieren einer Azure-Funktionen-App als API
> * Anfügen einer Azure-Funktionen-App an eine API
> * Anzeigen des neuen Hostschlüssels der Azure-Funktionen-App und des benannten Azure API Management-Werts
> * Testen der API im Azure-Portal

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie den Schnellstart unter [Schnellstart: Erstellen einer neuen Azure API Management-Dienstinstanz mithilfe des Azure-Portals](get-started-create-service-instance.md).
* Stellen Sie sicher, dass Ihr Abonnement eine Azure Functions-App enthält. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-get-started.md). Funktionen müssen einen HTTP-Trigger enthalten, und die Autorisierungsstufe muss auf *Anonym* oder *Funktion* festgelegt sein.

> [!NOTE]
> Sie können die API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs verwenden. Informationen zur Installation und zu den ersten Schritten finden Sie unter [Tutorial: Verwenden der API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs](visual-studio-code-tutorial.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importieren einer Azure-Funktionen-App als neue API

Führen Sie die folgenden Schritte aus, um auf der Grundlage einer Azure-Funktionen-App eine neue API zu erstellen.

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.

2. Wählen Sie in der Liste **Neue API hinzufügen** die Option **Funktionen-App** aus.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Screenshot: Kachel „Funktions-App“":::

3. Klicken Sie auf **Durchsuchen**, um eine Funktionen-App für den Import auszuwählen.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Screenshot, auf dem die Schaltfläche „Durchsuchen“ hervorgehoben ist":::

4. Klicken Sie auf den Abschnitt **Funktionen-App**, um in der Liste der verfügbaren Funktionen-Apps eine App auszuwählen.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Screenshot, auf dem der Abschnitt „Funktions-App“ hervorgehoben ist":::

5. Suchen Sie die Funktionen-App, aus der Sie Funktionen importieren möchten, klicken Sie darauf, und klicken Sie auf **Auswählen**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Screenshot, auf dem die Funktions-App, aus der Sie Funktionen importieren möchten, und die Schaltfläche „Auswählen“ hervorgehoben sind":::

6. Wählen Sie die Funktionen aus, die Sie importieren möchten, und klicken Sie auf **Auswählen**.
    * Sie können Funktionen nur importieren, wenn sie auf einem HTTP-Trigger mit der Autorisierungsstufe *Anonym* oder *Funktion* basieren.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Screenshot, auf dem die zu importierenden Funktionen und die Schaltfläche „Auswählen“ hervorgehoben sind":::

7. Wechseln Sie zur Ansicht **Vollständig**, und weisen Sie Ihrer neuen API **Produkt** zu. 
8. Geben Sie bei Bedarf während der Erstellung andere Felder an, oder konfigurieren Sie sie später über die Registerkarte **Einstellungen**. 
    * Die Einstellungen werden im Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md#import-and-publish-a-backend-api) erläutert.

    >[!NOTE]
    > Produkte sind Zuordnungen einer oder mehrerer APIs, die Entwicklern über das Entwicklerportal angeboten werden. Entwickler müssen zunächst ein Produkt abonnieren, um Zugriff auf die API zu erhalten. Wenn sie ein Produkt abonniert haben, erhalten sie einen Abonnementschlüssel für jede API in diesem Produkt. Als Ersteller der API Management-Instanz sind Sie Administrator und haben dadurch standardmäßig alle Produkte abonniert.
    >
    > Jede API Management-Instanz enthält zwei Standardbeispielprodukte:
    > - **Starter**
    > - **Unbegrenzt**

9. Klicken Sie auf **Erstellen**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Anfügen einer Azure-Funktionen-App an eine vorhandene API

Führen Sie die folgenden Schritte aus, um eine Azure-Funktionen-App zu einer vorhandenen API hinzuzufügen.

1. Klicken Sie in Ihrer **Azure API Management**-Dienstinstanz im Menü auf der linken Seite auf **APIs**.

2. Wählen Sie eine API aus, in die Sie eine Azure-Funktionen-App importieren möchten. Klicken Sie auf **...** , und klicken Sie im Kontextmenü auf **Importieren**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Screenshot, auf dem die Menüoption „Importieren“ hervorgehoben ist":::

3. Klicken Sie auf die Kachel **Funktionen-App**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Screenshot, auf dem die Kachel „Funktions-App“ hervorgehoben ist":::

4. Klicken Sie im Popupfenster auf **Durchsuchen**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Screenshot: Schaltfläche „Durchsuchen“":::

5. Klicken Sie auf den Abschnitt **Funktionen-App**, um in der Liste der verfügbaren Funktionen-Apps eine App auszuwählen.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Screenshot, auf dem die Liste der Funktions-Apps hervorgehoben ist":::

6. Suchen Sie die Funktionen-App, aus der Sie Funktionen importieren möchten, klicken Sie darauf, und klicken Sie auf **Auswählen**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Screenshot, auf dem die Funktions-App hervorgehoben ist, aus der Sie Funktionen importieren möchten":::

7. Wählen Sie die Funktionen aus, die Sie importieren möchten, und klicken Sie auf **Auswählen**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Screenshot: Hervorgehobene zu importierende Funktionen":::

8. Klicken Sie auf **Importieren**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Anfügen aus einer Funktionen-App":::

## <a name="authorization"></a><a name="authorization"></a> Autorisierung

Beim Importieren einer Azure-Funktionen-App wird automatisch Folgendes generiert:

* Hostschlüssel in der Funktionen-App mit dem Namen „apim-{*Name Ihrer Azure API Management-Dienstinstanz*}“
* Benannter Wert in der Azure API Management-Instanz mit dem Namen „{*Name der Instanz Ihrer Azure-Funktionen-App*}-key“, die den erstellten Hostschlüssel enthält

Bei APIs, die nach dem 4. April 2019 erstellt werden, wird der Hostschlüssel in HTTP-Anforderungen von API Management in einem Header an die Funktionen-App übergeben. Bei älteren APIs wird der Hostschlüssel als [Abfrageparameter](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization) übergeben. Sie können dieses Verhalten durch den `PATCH Backend`-[REST-API-Aufruf](/rest/api/apimanagement/2020-12-01/backend/update#backendcredentialscontract) für die der Funktions-App zugeordnete *Back-End-Entität* ändern.

> [!WARNING]
> Wenn Sie den Wert des Hostschlüssels der Azure-Funktions-App oder den benannten Wert von Azure API Management entfernen oder ändern, wird die Kommunikation zwischen den Diensten unterbrochen. Die Werte werden nicht automatisch synchronisiert.
>
> Falls Sie den Hostschlüssel rotieren müssen, ändern Sie auch den benannten Wert in Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Zugreifen auf den Hostschlüssel der Azure-Funktionen-App

1. Navigieren Sie zur Instanz Ihrer Azure-Funktionen-App.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Screenshot: Hervorgehobene Auswahl Ihrer Funktions-App-Instanz":::

2. Wählen Sie im seitlichen Navigationsmenü im Abschnitt **Funktionen** die Option **App-Schlüssel** aus.

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Screenshot, auf dem die Option „Funktions-App-Einstellungen“ hervorgehoben ist":::

3. Die Schlüssel finden Sie im Abschnitt **Hostschlüssel**.

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Screenshot, in dem der Abschnitt „Hostschlüssel“ hervorgehoben ist":::

### <a name="access-the-named-value-in-azure-api-management"></a>Zugreifen auf den benannten Wert in Azure API Management

Navigieren Sie zu Ihrer Azure API Management-Instanz, und klicken Sie im Menü auf der linken Seite auf **Benannte Werte**. Der Schlüssel der Azure-Funktionen-App ist dort gespeichert.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Hinzufügen aus einer Funktionen-App":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Testen der neuen API im Azure-Portal

Sie können Vorgänge direkt über das Azure-Portal aufrufen. Das Azure-Portal bietet eine komfortable Möglichkeit, die Vorgänge einer API anzuzeigen und zu testen.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Screenshot: Hervorgehobenes Testverfahren":::

1. Wählen Sie die im vorherigen Abschnitt erstellte API aus.

2. Wählen Sie die Registerkarte **Testen** aus.

3. Wählen Sie den Vorgang aus, den Sie testen möchten.

    * Auf der Seite werden Felder für Abfrageparameter und Header angezeigt. 
    * Einer der Header ist „Ocp-Apim-Subscription-Key“. Er steht für den Abonnementschlüssel des Produkts, das dieser API zugeordnet ist. 
    * Als Ersteller der API Management-Instanz sind Sie bereits Administrator, sodass der Schlüssel automatisch eingetragen wird. 

4. Wählen Sie **Send** (Senden) aus.

    * Wenn der Test erfolgreich ist, antwortet das Back-End mit **200 OK** und einigen Daten.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
