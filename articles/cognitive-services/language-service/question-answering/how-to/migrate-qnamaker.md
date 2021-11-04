---
title: Migrieren von QnA Maker-Wissensdatenbanken zu „Benutzerdefinierte Fragen und Antworten“
description: Migrieren Sie Ihre älteren QnA Maker-Wissensdatenbanken zu „Benutzerdefinierte Fragen und Antworten“, um die neuesten Features zu nutzen.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.author: diagarw
author: DishaAgarwal
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2ffd43a2747d462257b4223285b12b5f81e3e4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095635"
---
# <a name="migrate-from-qna-maker-to-custom-question-answering"></a>Migrieren von QnA Maker zu „Benutzerdefinierte Fragen und Antworten“

„Benutzerdefinierte Fragen und Antworten“ wurde im Mai 2021 mit mehreren neuen Features eingeführt, einschließlich verbesserter Relevanz mithilfe eines Deep Learning-Bewerters, präziser Antworten und End-to-End-Unterstützung für Regionen. Jedes Projekt von „Benutzerdefinierte Fragen und Antworten“ entspricht einer Wissensdatenbank in QnA Maker. Sie können Wissensdatenbanken problemlos von einer QnA Maker-Ressource zu „Benutzerdefinierte Fragen und Antworten“-Projekten innerhalb einer [Sprachressource](https://aka.ms/create-language-resource) migrieren. Sie können auch Wissensdatenbanken aus mehreren QnA Maker-Ressourcen in eine bestimmte Sprachressource migrieren.

Um Wissensdatenbanken erfolgreich zu migrieren, muss **das Konto, das die Migration durchführt, über Zugriff auf die ausgewählte QnA Maker- und Sprachressource verfügen**. Wenn eine Wissensdatenbank migriert wird, werden die folgenden Details in das neue „Benutzerdefinierte Fragen und Antworten“-Projekt kopiert:

- QnA-Paare, einschließlich der Vorschläge für aktives Lernen.
- Synonyme und Standardantwort von der QnA Maker-Ressource.
- Der Name der Wissensdatenbank wird in das Projektbeschreibungsfeld kopiert.

Einstellungen auf Ressourcenebene wie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) werden nicht zur neuen Ressource migriert. Diese Einstellungen auf Ressourcenebene müssen nach der Migration für die Sprachressource neu konfiguriert werden. Außerdem müssen Sie die Analyse für die Sprachressource [erneut aktivieren](analytics.md).

## <a name="steps-to-migrate"></a>Migrationsschritte

Führen Sie die folgenden Schritte für die Migration von Wissensdatenbanken aus:

1. Erstellen Sie vorab eine [Sprachressource](https://aka.ms/create-language-resource) mit aktivierter Option „Benutzerdefinierte Fragen und Antworten“. Wenn Sie die Sprachressource im Azure-Portal erstellen, wird die Option zum Aktivieren der benutzerdefinierten Fragen und Antworten angezeigt. Wenn Sie diese Option auswählen und fortfahren, werden Sie nach Azure Search-Details gefragt, um die Wissensdatenbanken zu speichern.

2. Wenn Sie Ihrer Sprachressource Wissensdatenbanken in mehreren Sprachen hinzufügen möchten, besuchen Sie [Language Studio](https://lanuage.azure.com), um Ihr erstes Projekt für „Benutzerdefinierte Fragen und Antworten“ zu erstellen, und wählen Sie die erste Option wie unten dargestellt aus. Spracheinstellungen für die Sprachressource können nur beim Erstellen eines Projekts angegeben werden. Wenn Sie vorhandene Wissensdatenbanken in einer einzelnen Sprache zur Sprachressource migrieren möchten, können Sie diesen Schritt überspringen.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Bildschirm zum Auswählen der Benutzeroberfläche für die Sprache](../media/migrate-qnamaker/choose-language.png)

3. Besuchen Sie [https://www.qnamaker.ai](https://www.qnamaker.ai), und wählen Sie **Migration starten** im Migrationshinweis auf der Wissensdatenbankseite aus. Es wird ein Dialogfeld zum Initiieren der Migration geöffnet.

   :::image type="content" source="../media/migrate-qnamaker/start-migration.png" alt-text="Schaltfläche „Migration starten“, die in einem Banner auf „qnamaker.ai“ angezeigt wird" lightbox="../media/migrate-qnamaker/start-migration.png":::

4. Geben Sie die erforderlichen Daten ein, um die Migration zu initiieren. Der Mandant wird automatisch ausgewählt. Sie können den Mandanten bei Bedarf wechseln.

   > [!div class="mx-imgBorder"]
   > ![Migrieren von QnA Maker mit rotem Auswahlfeld um die Option zum Auswählen des Mandanten](../media/migrate-qnamaker/tenant-selection.png)

5. Wählen Sie die QnA Maker-Ressource aus, die die zu migrierenden Wissensdatenbanken enthält.

   > [!div class="mx-imgBorder"]
   > ![Migrieren von QnA Maker mit rotem Auswahlfeld um die Auswahloption für die QnA Maker-Ressource](../media/migrate-qnamaker/select-resource.png)

6. Wählen Sie die Sprachressource aus, zu der Sie die Wissensdatenbanken migrieren möchten. Sie können nur die Sprachressourcen sehen, für die die Option „Benutzerdefinierte Fragen und Antworten“ aktiviert ist. Die Spracheinstellung für die Sprachressource wird in den Optionen angezeigt. Sie können Wissensdatenbanken in mehreren Sprachen aus QnA Maker-Ressourcen nicht zu einer Sprachressource migrieren, wenn deren Spracheinstellung nicht angegeben ist.

   > [!div class="mx-imgBorder"]
   > ![Migrieren von QnA Maker mit einem roten Auswahlfeld um die Option für die Sprachressource. Die aktuell ausgewählte Ressource enthält die Information, dass die Sprache nicht angegeben ist.](../media/migrate-qnamaker/language-setting.png)

    Wenn Sie Wissensdatenbanken in mehreren Sprachen zur Sprachressource migrieren möchten, müssen Sie die Einstellung für mehrere Sprachen aktivieren, wenn Sie das erste Projekt für „Benutzerdefinierte Fragen und Antworten“ für die Sprachressource erstellen. Befolgen Sie hierzu die Anweisungen in Schritt 2. **Wenn die Spracheinstellung für die Sprachressource nicht angegeben ist, wird ihr die Sprache der ausgewählten QnA Maker-Ressource zugewiesen**.

7. Wählen Sie alle zu migrierenden Wissensdatenbanken und dann **Weiter** aus.

   > [!div class="mx-imgBorder"]
   > ![Migrieren von QnA Maker mit einem roten Auswahlfeld um die Auswahloption für die Wissensdatenbank mit einer Dropdownliste, die drei Namen von Wissensdatenbanken anzeigt.](../media/migrate-qnamaker/select-knowledge-bases.png)

8. Sie können die Wissensdatenbanken überprüfen, die Sie migrieren möchten. Es könnten einige Überprüfungsfehler in den Projektnamen auftreten, da wir strengere Überprüfungsregeln für Projekte vom Typ „Benutzerdefinierte Fragen und Antworten“ befolgen. 

    > [!CAUTION]
    > Wenn Sie eine Wissensdatenbank mit demselben Namen wie ein Projekt migrieren, das bereits in der Zielsprachressource vorhanden ist, wird **der Inhalt des Projekts durch den Inhalt der ausgewählten Wissensdatenbank überschrieben**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Fehlermeldung mit der Information, dass Projektnamen keine Sonderzeichen enthalten dürfen](../media/migrate-qnamaker/special-characters.png)

9. Nachdem Sie etwaige Überprüfungsfehler behoben haben, wählen Sie **Weiter** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Entfernte Sonderzeichen](../media/migrate-qnamaker/validation-errors.png)

10. Es dauert einige Minuten, bis die Migration erfolgt. Brechen Sie die Migration nicht ab, während sie noch andauert. Sie können nach der Migration zu den migrierten Projekten innerhalb von [Language Studio](https://lanuage.azure.com) navigieren.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Erfolgreich migrierte Wissensdatenbanken mit Informationen, die Sie mithilfe von Language Studio veröffentlichen können](../media/migrate-qnamaker/migration-success.png)

    Wenn eine Wissensdatenbank nicht zu einem Projekt vom Typ „Benutzerdefinierte Fragen und Antworten“ migriert werden kann, wird eine Fehlermeldung angezeigt. Die häufigsten Migrationsfehler treten in folgenden Situationen auf:
    
    - Ihre Quell- und Zielressourcen sind ungültig.
    - Sie versuchen, eine leere Wissensdatenbank (KB) zu migrieren.
    - Sie haben den Grenzwert für eine Azure Search-Instanz erreicht, die mit Ihren Zielressourcen verknüpft ist.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Fehlerhafte Migration mit einem Beispielfehler](../media/migrate-qnamaker/migration-errors.png)

    Nachdem Sie diese Fehler behoben haben, können Sie die Migration erneut ausführen.

11. Bei der Migration werden nur die Testinstanzen Ihrer Wissensdatenbanken kopiert. Nach Abschluss der Migration müssen Sie die Wissensdatenbanken manuell bereitstellen, um den Testindex in den Produktionsindex zu kopieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Analysen mit [Azure Monitor-Diagnoseprotokollen](analytics.md) erneut aktivieren.
