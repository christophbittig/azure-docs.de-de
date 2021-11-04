---
title: 'Abrufen der Standardantwort: Benutzerdefinierte Fragen und Antworten'
description: Die Standardantwort wird zurückgegeben, wenn keine Entsprechung für die Frage vorliegt. Sie können die Standardantwort für benutzerdefinierte Fragen und Antworten bei Bedarf anpassen.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 94925a2a6cadc80a576470fc1d0a585bd4bb4a01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095646"
---
# <a name="change-default-answer-for-question-answering"></a>Ändern der Standardantwort für Fragen und Antworten

Wird auf eine Frage keine Antwort gefunden, sollte die Standardantwort für ein Projekt zurückgegeben werden. Wenn Sie eine Clientanwendung verwenden, z. B. den [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna), verfügt diese möglicherweise über eine separate Standardantwort, in der die Information enthalten ist, dass keine Antwort den Bewertungsschwellenwert erreicht hat.

## <a name="types-of-default-answer"></a>Arten von Standardantworten

In Ihrer Wissensdatenbank gibt es zwei Arten von Standardantworten. Es ist wichtig zu verstehen, wie und wann die einzelne Standardantwort von einer Vorhersageabfrage zurückgegeben wird:

|Arten von Standardantworten|Beschreibung der Antwort|
|--|--|
|Antwort der Wissensdatenbank, wenn keine Antwort ermittelt wird|`No good match found in KB.`: Findet die „Fragen und Antworten“-API keine passende Antwort auf die Frage, wird eine Standardtextantwort angezeigt. Für benutzerdefinierte Fragen und Antworten können Sie diesen Text in den **Einstellungen** Ihres Projekts festlegen. |
|Anweisungstext der Folgeäußerung|Wenn Sie eine Folgeäußerung in einem Konversationsfluss verwenden, benötigen Sie möglicherweise keine Antwort im QnA-Paar, da Sie möchten, dass der Benutzer aus den Folgeäußerungen auswählen kann. Legen Sie in diesem Fall bestimmten Text fest, indem Sie den Standardantworttext festlegen, der bei jeder Vorhersage für Folgeäußerungen zurückgegeben wird. Der Text soll als Anweisungstext zur Auswahl von Folgeäußerungen angezeigt werden. Ein Beispiel für diesen Standardantworttext ist `Please select from the following choices`. Diese Konfiguration wird in den nächsten Abschnitten dieses Dokuments erläutert. Sie können dies auch als Teil der Definition einer Wissensdatenbank mit `defaultAnswerUsedForExtraction` unter Verwendung der REST-API festlegen.|

### <a name="client-application-integration"></a>Clientanwendungsintegration

Für eine Clientanwendung, z. B. für einen Bot mit dem **Azure Bot Service**, können Sie aus den folgenden gängigen Szenarien auswählen:

* Verwenden der Einstellung Ihres Projekts/Ihrer Wissensdatenbank
* Verwenden Sie unterschiedlichen Text in der Clientanwendung, um zu unterscheiden, wenn eine Antwort zurückgegeben wird, diese aber nicht den Bewertungsschwellenwert erreicht. Bei diesem Text kann es sich entweder um statischen Text handeln, der im Code gespeichert ist, oder er kann in der Einstellungsliste der Clientanwendung gespeichert werden.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Festlegen der Standardantwort der Folgeäußerung, wenn Sie eine Wissensdatenbank erstellen

Wenn Sie eine neue Wissensdatenbank erstellen, gehört der Standardantworttext zu den Einstellungen. Wenn Sie diesen nicht während der Erstellung festlegen, können Sie ihn später wie folgt ändern.

## <a name="change-follow-up-prompts-default-answer-in-language-studio"></a>Ändern der Standardantwort der Folgeäußerung in Language Studio

Die Standardantwort der Wissensdatenbank wird zurückgegeben, wenn von „Fragen und Antworten“ keine Antwort zurückgegeben wird.

1. Melden Sie sich bei [Language Studio](https://language.azure.com) an, und wählen Sie Ihr Projekt aus der Liste aus.
1. Klicken Sie in der Navigationsleiste auf der linken Seite auf **Einstellungen**.
1. Ändern Sie den Wert für **Standardantwort, wenn keine Antwort gefunden wurde**, und klicken Sie auf **Speichern**.

> [!div class="mx-imgBorder"]
> ![Screenshot der Projekteinstellungen mit rot umrandeter Standardantwort](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Wissensdatenbank](manage-knowledge-base.md)
