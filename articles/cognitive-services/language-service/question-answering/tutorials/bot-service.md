---
title: 'Tutorial: Erstellen eines FAQ-Bots mit „Fragen und Antworten“ und Azure Bot Service'
description: In diesem Tutorial erstellen Sie ein FAQ-Bot mit „Fragen und Antworten“ und Azure Bot Service ohne Code.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 3478ab7b9a9d1920bcc5dc61b108b80514f60aba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439061"
---
# <a name="tutorial-create-a-faq-bot"></a>Tutorial: Erstellen eines FAQ-Bots

Erstellen Sie mit benutzerdefinierten Fragen und Antworten und Azure [Bot Service](https://azure.microsoft.com/services/bot-service/) einen FAQ-Bot ohne Code.

In diesem Tutorial lernen Sie Folgendes:

<!-- green checkmark -->
> [!div class="checklist"]
> * Verknüpfen eines Fragen und Antworten-Projekts/einer Fragen und Antworten-Wissensdatenbank mit einem Azure Bot Service
> * Bereitstellen eines Bots
> * Chatten mit dem Bot im Webchat
> * Aktivieren des Bots in unterstützten Kanälen

## <a name="create-and-publish-a-knowledge-base"></a>Erstellen und Veröffentlichen einer Wissensdatenbank

Befolgen Sie die Anleitung im [Artikel zu den ersten Schritten](../how-to/create-test-deploy.md). Nachdem die Wissensdatenbank erfolgreich bereitgestellt wurde, können Sie Artikel durcharbeiten.

## <a name="create-a-bot"></a>Erstellen eines Bots

Nachdem Ihr Projekt bzw. Ihre Wissensdatenbank bereitgestellt wurde, können Sie auf der Seite **Wissensdatenbank bereitstellen** einen Bot erstellen:

* Sie können schnell mehrere Bots erstellen, die alle auf die gleiche Wissensdatenbank verweisen, und dabei verschiedene Regionen oder Tarife für die einzelnen Bots verwenden.

* Wenn Sie Änderungen an der Wissensdatenbank vornehmen und sie erneut bereitstellen, müssen Sie keine weiteren Schritte für den Bot ausführen. Er ist bereits für die Verwendung mit der Wissensdatenbank konfiguriert und funktioniert auch nach späteren Änderungen. Nach jeder Veröffentlichung einer Wissensdatenbank werden alle mit ihr verbundenen Bots automatisch aktualisiert.

1. Wählen Sie im Language Studio-Portal auf der „Fragen und Antworten“-Seite **Wissensdatenbank bereitstellen** die Option **Bot erstellen** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Benutzeroberfläche mit der Option zum Erstellen eines Bots in Azure.](../media/bot-service/create-bot-in-azure.png)

1. Für das Azure-Portal wird eine neue Browserregisterkarte mit der Erstellungsseite von Azure Bot Service geöffnet. Konfigurieren Sie Azure Bot Service.

    |Einstellung |Wert|
    |----------|---------|
    | Bothandle| Eindeutiger Bezeichner für den Bot. Dieser Wert darf mit dem Namen der App übereinstimmen. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcengruppe | Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. |
    | Ort | Wählen Sie den gewünschten Ort aus. |
    | Tarif | Auswählen eines Tarifs |
    |App-Name | App Service-Name für Ihren Bot |
    |SDK-Sprache | C# oder Node.js. Nachdem der Bot erstellt wurde, können Sie den Code in die lokale Entwicklungsumgebung herunterladen und den Entwicklungsprozess fortsetzen. |
    | QnA-Authentifizierungsschlüssel | Dieser Schlüssel wird automatisch anhand des bereitgestellten Fragen und Antworten-Projekts/der Fragen und Antworten-Wissensdatenbank ausgefüllt. |
    | App Service-Plan/Standort | Dieser Wert wird automatisch eingetragen. Ändern Sie diesen Wert nicht. |

1. Öffnen Sie nach der Erstellung des Bots die **Bot Service**-Ressource.
1. Wählen Sie unter **Einstellungen** die Option **In Webchat testen** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Azure Bot Service-Benutzeroberfläche mit der Schaltfläche zum Testen von Webchats.](../media/bot-service/test-in-web-chat.png)

1. Geben Sie bei der Chataufforderung **Nachricht eingeben** Folgendes ein:

    `How do I setup my surface book?`

    Der Chatbot antwortet mit einer Antwort aus Ihrer Wissensdatenbank.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Chatantwort des Bottests mit einer Frage und einer vom Bot generierten Antwort.](../media/bot-service/bot-chat.png)

## <a name="integrate-the-bot-with-channels"></a>Integrieren des Bots in Kanäle

Wählen Sie in der von Ihnen erstellten Bot Service-Ressource **Kanäle** aus. Sie können den Bot in zusätzlichen [unterstützten Kanälen](/azure/bot-service/bot-service-manage-channels) aktivieren.

   >[!div class="mx-imgBorder"]
   >![Screenshot der Integration in Teams mit Produktsymbolen.](../media/bot-service/channels.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die „Fragen und Antworten“- und Bot Service-Ressourcen, wenn Sie diese Anwendung nicht mehr verwenden möchten.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Ihren FAQ-Bot mit mehrteiligen Äußerungen anpassen.
> [!div class="nextstepaction"]
> [Mehrfachdurchlauf-Eingabeaufforderungen](guided-conversations.md)
