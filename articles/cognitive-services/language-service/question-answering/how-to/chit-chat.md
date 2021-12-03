---
title: Hinzufügen von Smalltalk zu einer benutzerdefinierten Wissensdatenbank zur Beantwortung von Fragen
titleSuffix: Azure Cognitive Services
description: Das Hinzufügen von Smalltalk zu Ihrem Bot macht ihn unterhaltsamer und interessanter, wenn Sie ein Projekt erstellen. Über benutzerdefinierte Fragen und Antworten können Sie Ihren Wissensdatenbanken ganz einfach vordefinierten Smalltalk hinzufügen.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: c925fdf1b5b53f703d7c996110fe1b6b5f52cde8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476583"
---
# <a name="use-chitchat-with-a-project"></a>Verwenden von Chitchat bei einem Projekt

Durch Smalltalk wird ein Bot unterhaltsamer und interessanter. Über das Smalltalk-Feature in benutzerdefinierten Fragen und Antworten können Sie Ihrer Wissensdatenbank (Knowledge Base, KB) ganz einfach vordefinierten Smalltalk hinzufügen. Dies kann ein Ausgangspunkt für die Persönlichkeit Ihres Bots sein und Ihnen die Zeit und die Kosten ersparen, diese Elemente von Grund auf neu zu schreiben.

Dieses Dataset enthält rund 100 Smalltalk-Szenarien mit dem Sprachstil mehrerer Personas – von professionell über freundlich bis hin zu witzig. Wählen Sie die Persona aus, die dem Sprachstil Ihres Bots am nächsten kommt. Bei einer Benutzerabfrage wird von Fragen und Antworten versucht, die bestmögliche bekannte Smalltalk-Frage und -Antwort zu finden.

Einige Beispiele für die verschiedenen Persönlichkeiten finden Sie unten. Sie können alle Persönlichkeiten-[Datasets](https://github.com/microsoft/botframework-cli/blob/main/packages/qnamaker/docs/chit-chat-dataset.md) einschließlich Details der Persönlichkeiten anzeigen.

Für die Benutzerabfrage von `When is your birthday?` hat jede Persönlichkeit eine stilisierte Antwort:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Persönlichkeit|Beispiel|
|--|--|
|Professionell|Das Alter trifft auf mich nicht wirklich zu.|
|Freundlich|Ich habe nicht wirklich ein Lebensalter.|
|Witzig|Ich bin alterslos.|
|Mitfühlend|Ich habe kein Alter.|
|Begeistert|Ich bin ein Bot, also habe ich kein Alter.|
||

## <a name="language-support"></a>Sprachunterstützung

Smalltalk-Datasets werden in folgenden Sprachen unterstützt:

|Sprache|
|--|
|Chinesisch|
|Englisch|
|Französisch|
|Deutschland|
|Italienisch|
|Japanisch|
|Koreanisch|
|Portugiesisch|
|Spanisch|

## <a name="add-chitchat-source"></a>Hinzufügen einer Smalltalk-Quelle
Nachdem Sie Ihr Projekt erstellt haben, können Sie im Bereich **Quellen verwalten** URL- und dateibasierte Quellen sowie Smalltalk hinzufügen.

> [!div class="mx-imgBorder"]
> ![Hinzufügen einer Smalltalk-Quelle](../media/chit-chat/add-source.png)

Wählen Sie die Persönlichkeit aus, die Sie als Smalltalk-Grundlage verwenden möchten.

> [!div class="mx-imgBorder"]
> ![Menü mit verschiedenen Smalltalk-Persönlichkeiten](../media/chit-chat/personality.png)

## <a name="edit-your-chitchat-questions-and-answers"></a>Bearbeiten Ihrer Smalltalk-Fragen und -Antworten

Beim Bearbeiten Ihrer Wissensdatenbank wird eine neue Smalltalk-Quelle angezeigt, die auf der von Ihnen ausgewählten Persönlichkeit basiert. Sie können jetzt geänderte Fragen hinzufügen oder die Antworten wie bei jeder anderen Quelle bearbeiten.

> [!div class="mx-imgBorder"]
> ![Bearbeiten von Smalltalk-Fragepaaren](../media/chit-chat/edit-chit-chat.png)

Durch Auswählen von **Spalten anzeigen** auf der Symbolleiste können Sie die Anzeige von Kontext und Metadaten aktivieren bzw. deaktivieren.

## <a name="add-more-chitchat-questions-and-answers"></a>Hinzufügen weiterer Smalltalk-Fragen und -Antworten

Sie können ein neues Smalltalk-Fragepaar hinzufügen, das nicht im vordefinierten Dataset enthalten ist. Achten Sie darauf, kein Fragepaar zu duplizieren, das bereits im Smalltalk-Dataset enthalten ist. Wenn Sie ein neues Smalltalk-Fragepaar hinzufügen, wird es Ihrer Quelle vom Typ **Editorial** hinzugefügt. Fügen Sie das Metadaten-Schlüssel-Wert-Paar „Editorial: chitchat“ hinzu, damit der Bewerter versteht, dass es sich hierbei um Smalltalk handelt:

:::image type="content" source="../media/chit-chat/add-new-chit-chat.png" alt-text="Hinzufügen von Frage-Antwort-Paaren für Smalltalk" lightbox="../media/chit-chat/add-new-chit-chat.png":::

## <a name="delete-chitchat-from-your-project"></a>Löschen von Smalltalk aus Ihrem Projekt

Wählen Sie den Bereich **Quellen verwalten** und anschließend Ihre Smalltalk-Quelle aus. Ihre spezifische Smalltalk-Quelle wird als TSV-Datei mit dem ausgewählten Persönlichkeitsnamen aufgelistet. Klicken Sie auf der Symbolleiste auf **Löschen**.

> [!div class="mx-imgBorder"]
> ![Löschen der Smalltalk-Quelle](../media/chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren einer Knowledge Base](./migrate-knowledge-base.md)
