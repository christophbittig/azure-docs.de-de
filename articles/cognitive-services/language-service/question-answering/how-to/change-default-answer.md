---
title: 'Abrufen der Standardantwort: Benutzerdefinierte Fragen und Antworten'
description: Die Standardantwort wird zurückgegeben, wenn keine Entsprechung für die Frage vorliegt. Sie können die Standardantwort für benutzerdefinierte Fragen und Antworten bei Bedarf anpassen.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
author: mrbullwinkle
ms:author: mbullwin
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 44f2ba3d3a1ae3e05cace797349a2680584a9aff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476564"
---
# <a name="change-default-answer-for-question-answering"></a>Ändern der Standardantwort für Fragen und Antworten

Wird auf eine Frage keine Antwort gefunden, sollte die Standardantwort für ein Projekt zurückgegeben werden. Wenn Sie eine Clientanwendung verwenden, z. B. den [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna), verfügt diese möglicherweise über eine separate Standardantwort, in der die Information enthalten ist, dass keine Antwort den Bewertungsschwellenwert erreicht hat.

## <a name="default-answer"></a>Standardantwort


|Standardantwort|Beschreibung der Antwort|
|--|--|
|Antwort der Wissensdatenbank, wenn keine Antwort ermittelt wird|`No good match found in KB.`: Findet die „Fragen und Antworten“-API keine passende Antwort auf die Frage, wird eine Standardtextantwort angezeigt. Bei Fragen und Antworten können Sie diesen Text in den **Einstellungen** Ihres Projekts festlegen. |

### <a name="client-application-integration"></a>Clientanwendungsintegration

Für eine Clientanwendung, z. B. für einen Bot mit dem **Azure Bot Service**, können Sie aus den folgenden gängigen Szenarien auswählen:

* Verwenden der Einstellung Ihres Projekts/Ihrer Wissensdatenbank
* Verwenden Sie unterschiedlichen Text in der Clientanwendung, um zu unterscheiden, wenn eine Antwort zurückgegeben wird, diese aber nicht den Bewertungsschwellenwert erreicht. Bei diesem Text kann es sich entweder um statischen Text handeln, der im Code gespeichert ist, oder er kann in der Einstellungsliste der Clientanwendung gespeichert werden.

## <a name="change-default-answer-in-language-studio"></a>Ändern der Standardantwort im Language Studio

Die Standardantwort der Wissensdatenbank wird zurückgegeben, wenn von „Fragen und Antworten“ keine Antwort zurückgegeben wird.

1. Melden Sie sich bei [Language Studio](https://language.azure.com) an, und wählen Sie Ihr Projekt aus der Liste aus.
1. Klicken Sie in der Navigationsleiste auf der linken Seite auf **Einstellungen**.
1. Ändern Sie den Wert für **Standardantwort, wenn keine Antwort gefunden wurde**, und klicken Sie auf **Speichern**.

> [!div class="mx-imgBorder"]
> ![Screenshot der Projekteinstellungen mit rot umrandeter Standardantwort](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Wissensdatenbank](manage-knowledge-base.md)
