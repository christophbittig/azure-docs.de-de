---
title: Hinzufügen strukturierter Unterhaltungen mit mehrteiligen Äußerungen
description: In diesem Tutorial erfahren Sie, wie Sie strukturierte Unterhaltungen mit mehrteiligen Äußerungen führen.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 952f642b20cab149547cee1443c7c2ca594e3b34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101721"
---
# <a name="add-guided-conversations-with-multi-turn-prompts"></a>Hinzufügen strukturierter Unterhaltungen mit mehrteiligen Äußerungen

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen neuer Frage-Antwort-Paare zu Ihrem vorhandenen Projekt
> * Hinzufügen von Folgeäußerungen zum Erstellen strukturierter Unterhaltungen
> * Testen der mehrteiligen Äußerungen

## <a name="prerequisites"></a>Voraussetzungen

 In diesem Tutorial verwenden wir [Surface Pen-FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98), um eine Wissensdatenbank zu erstellen.

Wenn Sie noch nie ein Fragen und Antworten-Projekt erstellt haben, wird empfohlen, mit dem Artikel für [erste Schritte](../how-to/create-test-deploy.md) zu beginnen, der Sie Schritt für Schritt durch den Prozess führt.

## <a name="view-question-answer-pair-context"></a>Anzeigen des Kontexts des Frage-Antwort-Paars

In diesem Beispiel gehen wir davon aus, dass Benutzer nach zusätzlichen Details zum Surface Pen-Produkt fragen (insbesondere zur Problembehandlung), aber nicht die richtigen Antworten erhalten. Deshalb fügen wir weitere Äußerungen hinzu, um zusätzliche Szenarien zu unterstützen und die Benutzer mithilfe der mehrteiligen Äußerungen zu den richtigen Antworten weiterzuleiten.

Mehrteilige Äußerungen, die Frage-Antwort-Paaren zugeordnet sind, können angezeigt werden, indem Sie **Spalten anzeigen** > **Kontext** auswählen. Standardmäßig sollte dies bereits auf der Seite **Wissensdatenbank bearbeiten** auf der Language Studio-Benutzeroberfläche für Fragen und Antworten aktiviert sein.

> [!div class="mx-imgBorder"]
> [ ![Screenshot der Benutzeroberfläche mit der Option „Spalten anzeigen“ und rot hervorgehobenem Kontext]( ../media/guided-conversations/context.png) ]( ../media/guided-conversations/context.png#lightbox)

Dadurch wird die Kontextstruktur mit allen Folgeäußerungen angezeigt, die mit einem F&A-Paar verknüpft sind: 

> [!div class="mx-imgBorder"]
> [ ![Screenshot der FAQ-Seite von Surface Pen mit Antworten auf häufig gestellte Fragen ]( ../media/guided-conversations/surface-source.png) ]( ../media/guided-conversations/surface-source.png#lightbox)

## <a name="add-question-pair-with-follow-up-prompts"></a>Hinzufügen eines Fragepaars mit Folgeäußerungen

Um Benutzern bei der Lösung von Problemen mit dem Surface Pen zu unterstützen, fügen wir Folgeäußerungen hinzu:

- Hinzufügen eines neuen Fragepaars mit zwei Folgeäußerungen
- Hinzufügen einer Folgeäußerung zu einer der neu hinzugefügten Äußerungen

1. Hinzufügen eines neuen F&A-Paars mit zwei Folgeäußerungen: **Kompatibilität überprüfen** und **Stifteinstellungen überprüfen** Mithilfe des Editors fügen wir ein neues F&A-Paar mit einer Folgeäußerung hinzu, indem wir auf **Fragepaar hinzufügen** klicken.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Benutzeroberfläche mit rot umrandetem Feld „Fragepaar hinzufügen“]( ../media/guided-conversations/add-question.png)
    
    Im **Editorial** wird eine neue Zeile erstellt, in der wir das Frage-Antwort-Paar wie nachfolgend dargestellt eingeben:
    
    |Feld|Wert|
    |-----|----|
    |Fragen | Beheben von Problemen mit Surface |
    |Antworten und Äußerungen | Im Anschluss finden Sie einige Hinweise, die Sie zuerst ausprobieren sollten, wenn Surface Pen nicht schreibt, keine Apps öffnet oder keine Verbindung mit Bluetooth herstellt.|
    
2. Anschließend fügen wir dem neu erstellten Fragepaar eine Folgeäußerung hinzu, indem wir **Folgeäußerungen hinzufügen** auswählen. Füllen Sie die Details für die Äußerung wie gezeigt aus:
    
    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Benutzeroberfläche mit rot umrandetem Feld „Folgeäußerung hinzufügen“]( ../media/guided-conversations/add-prompts.png) ]( ../media/guided-conversations/add-prompts.png#lightbox)
    
    Wir stellen **Kompatibilität überprüfen** als „Anzeigetext“ für die Äußerung zur Verfügung und versuchen, sie mit einem F&A-Paar zu verknüpfen. Da kein entsprechendes F&A-Paar verfügbar ist, das mit der Äußerung verknüpft werden kann, wenn wir nach „Surface Pen-Kompatibilität überprüfen“ suchen, erstellen wir ein neues Fragepaar, indem wir auf **Link zum neuen Link erstellen** klicken und **Fertig** auswählen. Wählen Sie anschließend **Änderungen speichern** aus.
    
    > [!div class="mx-imgBorder"]
    > [ ![Screenshot einer Frage und Antwort in Verbindung mit der Kompatibilitätsüberprüfung Ihres Surface Pens]( ../media/guided-conversations/compatability-check.png) ]( ../media/guided-conversations/compatability-check.png#lightbox)
    
3. Auf ähnliche Weise fügen wir eine weitere Äußerung **Stifteinstellungen überprüfen** hinzu, um den Benutzer bei der Problembehandlung des Produkts zu unterstützen. Auch hier fügen wir wieder ein Fragepaar hinzu.
    
    > [!div class="mx-imgBorder"]
    > [ ![Screenshot einer Frage und Antwort in Verbindung mit der Einstellungsüberprüfung Ihres Surface Pens]( ../media/guided-conversations/pen-settings.png) ]( ../media/guided-conversations/check-pen-settings.png#lightbox)

4. Fügen Sie eine weitere Folgeäußerung zur neu erstellten Äußerung hinzu. Wir fügen nun „Stiftspitze ersetzen“ als Folgeäußerung zu der zuvor erstellten Äußerung „Stifteinstellungen überprüfen“ hinzu.

    > [!div class="mx-imgBorder"]
    > [ ![Screenshot einer Frage und Antwort zum Erstellen einer Folgeäußerung zum Ersetzen von Stiftspitzen]( ../media/guided-conversations/replace-pen.png) ]( ../media/guided-conversations/replace-pen.png#lightbox)
    
5. Speichern Sie die Änderungen, und testen Sie die Äußerungen im Bereich **Test**:
    
    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Benutzeroberfläche für Fragen und Antworten mit rot umrandeten Feld „Änderungen speichern“]( ../media/guided-conversations/save-changes.png) ]( ../media/guided-conversations/save-changes.png#lightbox)
    
    Für die Benutzerabfrage **Probleme mit dem Surface Pen** gibt das System eine Antwort zurück und stellt dem Benutzer die neu hinzugefügten Äußerungen vor. Der Benutzer wählt dann eine der Äußerungen aus **(Stifteinstellungen überprüfen)** und daraufhin wird ihm die zugehörige Antwort mit einer weiteren Äußerung **(Stiftspitze ersetzen)** zurückgegeben. Wenn diese Option ausgewählt wird, erhält der Benutzer weitere Informationen. Mehrteilige Äußerungen werden also verwendet, um den Benutzer bei der Suche nach der benötigten Antwort zu unterstützen.
    
    > [!div class="mx-imgBorder"]
    > [ ![Screenshot der Benutzeroberfläche für den Chattest.]( ../media/guided-conversations/test.png) ]( ../media/guided-conversations/test.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erweitern Ihrer Wissensdatenbank durch aktives Lernen](active-learning.md)
