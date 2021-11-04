---
title: 'Abrufen der Standardantwort: QnA Maker'
description: Die Standardantwort wird zurückgegeben, wenn keine Entsprechung für die Frage vorliegt. Sie können die Standardantwort bei Bedarf anpassen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7c73b08a634d1b68c3d9275dc39cc7cd95d51a6f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069271"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Ändern der Standardantwort für eine QnA Maker-Ressource

Die Standardantwort für eine Wissensdatenbank sollte zurückgegeben werden, wenn keine Antwort gefunden wird. Wenn Sie eine Clientanwendung verwenden, z. B. den [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna), verfügt diese möglicherweise über eine separate Standardantwort, in der die Information enthalten ist, dass keine Antwort den Bewertungsschwellenwert erreicht hat.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="types-of-default-answer"></a>Arten von Standardantworten

In Ihrer Wissensdatenbank gibt es zwei Arten von Standardantworten. Es ist wichtig zu verstehen, wie und wann die einzelne Standardantwort von einer Vorhersageabfrage zurückgegeben wird:

|Arten von Standardantworten|Beschreibung der Antwort|
|--|--|
|Antwort der Wissensdatenbank, wenn keine Antwort ermittelt wird|`No good match found in KB.`: Wenn die [GenerateAnswer-API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) keine passende Antwort auf die Frage findet, wird die `DefaultAnswer`-Einstellung des App-Diensts zurückgegeben. Alle Wissensdatenbanken in derselben QnA Maker-Ressource verwenden denselben Standardantworttext.<br>Sie können die Einstellung im Azure-Portal, über den App-Dienst oder mit den REST-APIs zum [Abrufen](/rest/api/appservice/webapps/listapplicationsettings) oder [Aktualisieren](/rest/api/appservice/webapps/updateapplicationsettings) der Einstellung verwalten.|
|Anweisungstext der Folgeäußerung|Wenn Sie eine Folgeäußerung in einem Konversationsfluss verwenden, benötigen Sie möglicherweise keine Antwort im QnA-Paar, da Sie möchten, dass der Benutzer aus den Folgeäußerungen auswählen kann. Legen Sie in diesem Fall bestimmten Text fest, indem Sie den Standardantworttext festlegen, der bei jeder Vorhersage für Folgeäußerungen zurückgegeben wird. Der Text soll als Anweisungstext zur Auswahl von Folgeäußerungen angezeigt werden. Ein Beispiel für diesen Standardantworttext ist `Please select from the following choices`. Diese Konfiguration wird in den nächsten Abschnitten dieses Dokuments erläutert. Kann auch als Teil der Wissensdatenbankdefinition von `defaultAnswerUsedForExtraction` unter Verwendung der [REST-API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create) festgelegt werden.|

### <a name="client-application-integration"></a>Clientanwendungsintegration

Für eine Clientanwendung, z. B. für einen Bot mit dem **Azure Bot Service**, können Sie aus den folgenden gängigen Szenarien auswählen:

* Verwenden der Einstellung der Wissensdatenbank
* Verwenden Sie unterschiedlichen Text in der Clientanwendung, um zu unterscheiden, wenn eine Antwort zurückgegeben wird, diese aber nicht den Bewertungsschwellenwert erreicht. Bei diesem Text kann es sich entweder um statischen Text handeln, der im Code gespeichert ist, oder er kann in der Einstellungsliste der Clientanwendung gespeichert werden.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Festlegen der Standardantwort der Folgeäußerung, wenn Sie eine Wissensdatenbank erstellen

Wenn Sie eine neue Wissensdatenbank erstellen, gehört der Standardantworttext zu den Einstellungen. Wenn Sie diesen nicht während der Erstellung festlegen, können Sie ihn später wie folgt ändern.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Ändern der Standardantwort der Folgeäußerung im QnA Maker-Portal

Die Standardantwort der Wissensdatenbank wird zurückgegeben, wenn keine Antwort vom QnA Maker-Dienst zurückgegeben wird.

1. Melden Sie sich beim [QnA Maker](https://www.qnamaker.ai/)-Portal an, und wählen Sie die Wissensdatenbank aus der Liste aus.
1. Klicken Sie in der Navigationsleiste auf **Einstellungen**.
1. Ändern Sie den Wert **Default answer text** (Standardantworttext) im Abschnitt **Manage knowledge base** (Wissensdatenbank verwalten).

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Screenshot: QnA Maker-Portal, Einstellungsseite, Textfeld für die Standardantwort hervorgehoben":::

1. Klicken Sie auf **Speichern und Trainieren**, um die Änderungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Wissensdatenbank](../How-to/manage-knowledge-bases.md)
