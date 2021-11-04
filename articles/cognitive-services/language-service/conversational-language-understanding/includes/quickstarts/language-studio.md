---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a8af7d5c76552ade3ffd5d50b308cee6b0b1446c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100871"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

## <a name="sign-in-to-language-studio"></a>Anmelden bei Language Studio

1. Wechseln Sie zu [Language Studio](https://aka.ms/languageStudio), und melden Sie sich mit Ihrem Azure-Konto an. 

2. Suchen Sie im angezeigten Fenster **Sprachressource auswählen** nach Ihrem Azure-Abonnement, und wählen Sie Ihre Sprachressource aus. Wenn keine Ressource vorhanden ist, können Sie eine neue erstellen.

    > [!NOTE]
    > Derzeit können nur Ressourcen mit dem Tarif Standard (**S**) mit dem Conversational Language Understanding-Dienst verwendet werden.
    
    :::image type="content" source="../../media/quickstart-language-resource.png" alt-text="Screenshot: Bildschirm für die Ressourcenauswahl in Language Studio" lightbox="../../media/quickstart-language-resource.png":::

## <a name="create-a-conversation-project"></a>Erstellen eines Unterhaltungsprojekts

Nachdem Sie Ihrem Konto eine Sprachressource zugeordnet haben, erstellen Sie ein Conversational Language Understanding-Projekt. In dieser Schnellstartanleitung erstellen Sie ein Projekt, das Befehle für E-Mails identifizieren kann, z. B. das Lesen von E-Mails durch bestimmte Personen, das Löschen von E-Mails und das Anfügen eines Dokuments an eine E-Mail.

1. Suchen Sie in [Language Studio](https://aka.ms/languageStudio) den Abschnitt **Unterhaltungssprache verstehen**, und klicken Sie auf **Conversational Language Understanding**. Dadurch gelangen Sie zur Seite für das **Unterhaltungsprojekt**. 
 
    
    :::image type="content" source="../../media/projects-page.png" alt-text="Screenshot: Seite für das Unterhaltungsprojekt in Language Studio" lightbox="../../media/projects-page.png":::

2. Klicken Sie auf **Neues Projekt erstellen**. Wählen Sie **Unterhaltungsprojekt** aus, und klicken Sie dann auf **Weiter**.

Anschließend müssen Sie die folgenden Details angeben:

|Wert  | BESCHREIBUNG  |
|---------|---------|
|Name     | Einen Namen für Ihr Projekt.        |
|BESCHREIBUNG    | Eine optionale Projektbeschreibung.        |
|Primäre Textsprache     | Die primäre Sprache Ihres Projekts. Ihre Trainingsdaten sollten hauptsächlich in dieser Sprache vorliegen. Wählen Sie für diese Schnellstartanleitung die Option **Englisch** aus.        |
|Mehrere Sprachen aktivieren     |  Gibt an, ob Sie ihrem Projekt die gleichzeitige Unterstützung mehrerer Sprachen ermöglichen möchten. Aktivieren Sie diese Option für diese Schnellstartanleitung.       |

Nachdem Sie damit fertig sind, klicken Sie auf **Weiter**, überprüfen die Details und klicken dann auf **Projekt erstellen**, um den Vorgang abzuschließen. Nun sollte der Bildschirm **Schemaerstellung** in Ihrem Projekt angezeigt werden.

## <a name="build-schema"></a>Schemaerstellung

1. Wählen Sie auf der Seite **Schemaerstellung** die Registerkarte „Absichten“ oder „Entitäten“ aus, und klicken Sie auf **Hinzufügen**. Sie werden zur Eingabe eines Namens aufgefordert, bevor Sie die Erstellung der Absicht oder Entität abschließen können.

2. Erstellen Sie drei Absichten mit den folgenden Namen:
    - **Lesen**
    - **Löschen**
    - **Anfügen**

3. Erstellen Sie drei Entitäten mit den folgenden Namen:
    - **Sender**
    - **FileName**
    - **FileType**


Wenn Sie auf die Absicht klicken, werden Sie zur Seite [Tag-Äußerungen](../../how-to/tag-utterances.md) weitergeleitet, auf der Sie Beispiele für Absichten hinzufügen und sie mit Entitäten bezeichnen können.


:::image type="content" source="../../media/quickstart-intents.png" alt-text="Screenshot: Schemaseite in Language Studio" lightbox="../../media/quickstart-intents.png":::

## <a name="tag-utterances"></a>Kennzeichnen von Äußerungen

Auf der Seite „Tag-Äußerungen“ fügen wir den Absichten einige Beispiele hinzu. Wählen Sie im Dropdownfeld die Absicht **Lesen** aus, die **Absicht auswählen** angibt.

Schreiben Sie in das Textfeld mit der Aufschrift **Schreiben Sie Ihre Beispieläußerung, und drücken Sie die Eingabetaste** den Satz *Lesen Sie die E-Mail von Carol*, und drücken Sie die Eingabetaste, um ihn als Beispieläußerung hinzuzufügen.

Ziehen Sie den Cursor über das Wort „*Carol*“, und wählen Sie die Entität **Absender** aus, um „*Carol*“ als Entität zu bezeichnen.

Fügen Sie die restlichen Äußerungen mit den folgenden Absichten und Entitäten hinzu.

|Äußerung|Intent|Entitäten|
|--|--|--|
|*Lesen Sie die E-Mail von John für mich.*|**Lesen**|„John“: **Absender**|
|*Was hat die E-Mail von Matt besagt?*|**Lesen**|„Matt“: **Absender**|
|*Löschen Sie meine letzte E-Mail von Martha*|**Löschen**|„Martha“: **Absender**|
|*Dies löschen*|**Löschen**|_Keine Entitäten_|
|*Diese entfernen*|**Löschen**|_Keine Entitäten_|
|*Dies in den gelöschten Ordner verschieben*|**Löschen**|_Keine Entitäten_|
|*Fügen Sie die Excel-Datei „reports q1“ an*|**Anfügen**|„excel“: **FileType**, <br> „reports q1“ -> **FileName**|
|*Fügen Sie die PowerPoint-Datei an*|**Anfügen**|„PowerPoint“: **FileType**|
|*Fügen Sie die PDF-Datei mit dem signierten Vertrag an* |**Anfügen**|„PDF“: **FileType**, <br> „Signierter Vertrag“: **FileName**|


Wenn Sie fertig sind, klicken Sie auf **Änderungen speichern**, um die Äußerungen und Bezeichnungen im Projekt zu speichern. Das Symbol neben der Schaltfläche wird grün, wenn die Änderungen gespeichert werden. Wechseln Sie als nächstes zur Seite **Modell trainieren**.

:::image type="content" source="../../media/quickstart-utterances.png" alt-text="Screenshot: Bildschirm zum Markieren von Absichten in Language Studio" lightbox="../../media/quickstart-utterances.png":::

## <a name="train-your-model-and-view-its-details"></a>Trainieren Ihres Modells und Anzeigen seiner Details

Klicken Sie links auf dem Bildschirm auf **Modell trainieren**. Zum Trainieren Ihres Modells müssen Sie einen Namen für das Modell angeben. Schreiben Sie einen Namen wie „*v0.1*“, und drücken Sie die EINGABETASTE. 

Deaktivieren Sie **Run evaluation with training** (Auswertung mit Training ausführen), bevor Sie auf **Trainieren** klicken. 

Daraufhin sollte die Seite **Modelldetails anzeigen** angezeigt werden. Warten Sie, bis das Training abgeschlossen ist. Dies kann etwa fünf Minuten dauern. Wenn das Training erfolgreich durchgeführt wurde, wählen Sie auf der linken Seite des Bildschirms **Modell bereitstellen** aus.

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

Wählen Sie auf der Seite **Modell bereitstellen** auf der linken Seite des Bildschirms das trainierte Modell aus, und klicken Sie auf die Schaltfläche **Modell bereitstellen**. Klicken Sie auf dem angezeigten Bildschirm auf **Bereitstellen**.

## <a name="test-your-model"></a>Testen des Modells

Klicken Sie links auf dem Bildschirm auf **Modell testen**, und wählen Sie den Modelllink aus. Geben Sie die Äußerung „*Dieses wegwerfen*“ ein, und klicken Sie auf **Test ausführen**. 

Die oberste Absicht wird nun als **Löschen** ohne Entitäten bezeichnet.

Sie können andere Äußerungen wie die folgenden testen:
* „*Fügen Sie meine DOCX-Datei an*“, 
* „*Lesen Sie die E-Mail von Jason*“, 
* „*Fügen Sie die PPT-Datei namens CLU-Demo an*“.

Sie können Äußerungen auch in anderen Sprachen wie den folgenden Ausdrücken testen:

* „*Joindre le fichier PDF*“ (Französisch für: „*PDF-Datei anfügen*“), 
* „*Lea el correo electrónico de Macy*“ (Spanisch für: „*Lesen Sie die E-Mail von Macy*“)
