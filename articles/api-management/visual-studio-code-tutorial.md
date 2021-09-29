---
title: 'Tutorial: Importieren und Verwalten von APIs – Azure API Management und Visual Studio Code | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie die Azure API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs verwendet wird.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: ecdb97ad62a9b75279546493bbbf185f92fd29f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625770"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Tutorial: Verwenden der API Management-Erweiterung für Visual Studio Code zum Importieren und Verwalten von APIs

In diesem Tutorial erfahren Sie, wie Sie die API Management-Erweiterung für Visual Studio Code für gängige Vorgänge in API Management verwenden. Verwenden Sie die vertraute Visual Studio Code-Umgebung, um APIs zu importieren, zu aktualisieren, zu testen und zu verwalten.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Importieren einer API in API Management
> * Bearbeiten der API
> * Anwenden von Richtlinien für API Management
> * Testen der API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API in API Management-Erweiterung":::

Eine Einführung in zusätzliche API Management-Funktionen finden Sie in den API Management-Tutorials im [Azure-Portal](import-and-publish.md).

## <a name="prerequisites"></a>Voraussetzungen
- Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
- Stellen Sie sicher, dass [Visual Studio Code](https://code.visualstudio.com/) und die neueste Version der [Azure API Management-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) installiert ist.
- [Erstellen einer API Management-Instanz](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importieren einer API

Im folgenden Beispiel wird eine OpenAPI-Spezifikation im JSON-Format in API Management importiert. Microsoft stellt die in diesem Beispiel verwendete Back-End-API bereit und hostet sie in Azure unter `https://conferenceapi.azurewebsites.net?format=json`.

1. Wählen Sie auf der Aktivitätsleiste in Visual Studio Code das Azure-Symbol aus.
1. Erweitern Sie im Explorer-Bereich die API Management-Instanz, die Sie erstellt haben.
1. Klicken Sie mit der rechten Maustaste auf **APIs**, und wählen Sie **Aus OpenAPI-Link importieren** aus. 
1. Geben Sie die folgenden Werte ein, wenn Sie dazu aufgefordert werden:
    1. Einen **OpenAPI-Link** für Inhalte im JSON-Format. In diesem Beispiel: *https://conferenceapi.azurewebsites.net?format=json* .
    Diese URL ist der Dienst, der die Beispiel-API implementiert. API Management leitet Anfragen an diese Adresse weiter.
    1. Einen **API-Namen**, z. B. *demo-conference-api*, der in der API Management-Instanz eindeutig ist. Der Name darf nur Buchstaben, Ziffern und Bindestriche enthalten. Das erste und das letzte Zeichen müssen alphanumerisch sein. Dieser Name wird im Pfad verwendet, um die API aufzurufen.

Nachdem die API erfolgreich importiert wurde, wird sie im Explorer-Bereich angezeigt, und die verfügbaren API-Vorgänge werden unter dem Knoten **Vorgänge** angezeigt.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Importierte API im Explorer-Bereich":::

## <a name="edit-the-api"></a>Bearbeiten der API

Sie können die API in Visual Studio Code bearbeiten. Bearbeiten Sie z. B. die Resource Manager-JSON-Beschreibung der API im Editor-Fenster, um das **http**-Protokoll zu entfernen, das für den Zugriff auf die API verwendet wird. Klicken Sie dann auf **Datei** > **Speichern**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Bearbeiten der JSON-Beschreibung":::

Um das OpenAPI-Format zu bearbeiten, klicken Sie im Explorer-Bereich mit der rechten Maustaste auf den API-Namen, und wählen Sie **OpenAPI bearbeiten** aus. Nehmen Sie die gewünschten Änderungen vor, und klicken Sie dann auf **Datei** > **Speichern**.

## <a name="apply-policies-to-the-api"></a>Anwenden von Richtlinien auf die API 

API Management stellt [Richtlinien](api-management-policies.md) bereit, die Sie für Ihre APIs konfigurieren können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Richtlinien können global sein und für alle APIs in Ihrer API Management-Instanz gelten, oder sie können auf eine bestimmte API oder einen API-Vorgang eingeschränkt werden.

In diesem Abschnitt wird gezeigt, wie Sie einige gängige Richtlinien für ausgehenden Datenverkehr auf Ihre API anwenden, die die API-Antwort transformieren. Die Richtlinien in diesem Beispiel ändern Antwortheader und blenden die ursprünglichen Back-End-URLs aus, die im Antworttext aufgeführt werden.

1. Wählen Sie im Explorer-Bereich unter *demo-conference-api* die Option **Richtlinie** aus. Die Richtliniendatei wird im Editor-Fenster geöffnet. Diese Datei konfiguriert Richtlinien für alle Vorgänge in der API. 

1. Aktualisieren Sie die Datei mit dem folgenden Inhalt im `<outbound>`-Element:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * Mit der ersten `set-header`-Richtlinie wird zu Demonstrationszwecken ein benutzerdefinierter Antwortheader hinzugefügt.
    * Die zweite `set-header`-Richtlinie löscht den **X-Powered-By**-Header, sofern vorhanden. Mit diesem Header kann das im API-Back-End verwendete Anwendungsframework angezeigt werden, und Herausgeber entfernen es häufig.
    * Die Richtlinie `redirect-content-urls` ändert (maskiert) Links im Antworttext, sodass diese über das API Management-Gateway auf den äquivalenten Link zeigen.
    
1. Speichern Sie die Datei . Wenn Sie dazu aufgefordert werden, wählen Sie **Hochladen** aus, um die Datei in die Cloud hochzuladen.

## <a name="test-the-api"></a>Testen der API

### <a name="get-the-subscription-key"></a>Abrufen des Abonnementschlüssels

Um die importierte API und die Richtlinien zu testen, die angewendet werden, benötigen Sie einen Abonnementschlüssel für Ihre API Management-Instanz.

1. Klicken Sie im Explorer-Bereich mit der rechten Maustaste auf den Namen Ihrer API Management-Instanz.
1. Wählen Sie **Abonnementschlüssel kopieren** aus.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Kopieren des Abonnementschlüssels":::

### <a name="test-an-api-operation"></a>Testen eines API-Vorgangs

1. Erweitern Sie im Explorer-Bereich den Knoten **Operations** unter der *demo-conference-api*, die Sie importiert haben.
1. Wählen Sie einen Vorgang wie *GetSpeakers* aus, klicken Sie dann mit der rechten Maustaste auf den Vorgang, und wählen Sie **Vorgang testen** aus.
1. Ersetzen Sie im Editor-Fenster `{{SubscriptionKey}}` neben **Ocp-Apim-Subscription-Key** durch den von Ihnen kopierten Abonnementschlüssel.
1. Klicken Sie auf **Anforderung senden**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Senden einer API-Anforderung aus Visual Studio Code":::

Wenn die Anforderung erfolgreich ist, antwortet das Back-End mit **200 OK** und einigen Daten.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API-Testvorgang":::

Beachten Sie die folgenden Details in der Antwort:
* Der Antwort wird der **benutzerdefinierte** Header hinzugefügt.
* Der **X-Powered-By**-Header wird nicht in der Antwort aufgeführt.
* URLs für das API-Back-End werden an das API Management-Gateway umgeleitet, in diesem Fall an `https://apim-hello-world.azure-api.net/demo-conference-api`.

### <a name="trace-the-api-operation"></a>Ablaufverfolgung des API-Vorgangs

Ausführliche Ablaufverfolgungsinformationen zum Debuggen des API-Vorgangs finden Sie unter dem Link neben **OCP-APIM-Trace-Location**. 

Die JSON-Datei an diesem Speicherort enthält Ablaufverfolgungsinformationen für eingehenden und ausgehenden Datenverkehr sowie für das Back-End, damit Sie ermitteln können, wo nach der Anforderung ggf. Probleme auftreten.

> [!TIP]
> Beim Testen von API-Vorgängen ermöglicht die API Management-Erweiterung optionales [Debuggen von Richtlinien](api-management-debug-policies.md) (verfügbar in der Dienstebene „Developer“).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die API Management-Instanz nicht mehr benötigen, können Sie diese entfernen, indem Sie mit der rechten Maustaste auf die Instanz klicken und **Im Portal öffnen** auswählen. Dort können Sie die [API Management-Instanz](get-started-create-service-instance.md#clean-up-resources) und deren Ressourcengruppe löschen.

Alternativ können Sie auf **Delete API Management** (API Management-Instanz löschen) klicken, um nur die API Management-Instanz zu löschen. Die Ressourcengruppe wird dabei nicht gelöscht.

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="API Management-Instanz aus Visual Studio Code löschen":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden mehrere Funktionen der API Management-Erweiterung für Visual Studio Code vorgestellt, mit denen Sie APIs importieren und verwalten können. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Importieren einer API in API Management
> * Bearbeiten der API
> * Anwenden von Richtlinien für API Management
> * Testen der API

Die API Management-Erweiterung bietet zusätzliche Funktionen für das Arbeiten mit Ihren APIs. Beispielsweise für das [Debuggen von Richtlinien](api-management-debug-policies.md) (verfügbar in der Dienstebene „Developer“) oder das Erstellen und Verwalten [benannter Werte](api-management-howto-properties.md).
