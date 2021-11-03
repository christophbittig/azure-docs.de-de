---
title: Verwalten von Wissensdatenbanken – Fragen und Antworten
description: Mit der benutzerdefinierten Fragen und Antworten können Sie Projekte verwalten, indem Sie Zugriff auf die Projekteinstellungen und -inhalte bereitstellen.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 1a28a0a4bf66824cc1c25d73512415e2a5613827
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095613"
---
# <a name="create-and-manage-project-settings"></a>Erstellen und Verwalten von Projekteinstellungen

Mit Fragen und Antworten können Sie Ihre Projekte/Wissensdatenbanken verwalten, indem Sie Zugriff auf die Projekteinstellungen und Datenquellen bereitstellen. Wenn Sie noch nie ein Fragen und Antworten-Projekt erstellt haben, wird empfohlen, mit dem [Artikel für erste Schritte](create-test-deploy.md) zu beginnen.

## <a name="prerequisites"></a>Voraussetzungen

> * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.
> * Die [Sprachressource](https://aka.ms/create-language-resource) mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“ im Azure-Portal Merken Sie sich die Azure Active Directory-ID, das Abonnement und den Sprachressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

## <a name="create-a-project"></a>Erstellen eines Projekts

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen im [Language Studio](https://language.azure.com/)-Portal an.

2. Öffnen Sie die Seite [Fragen und Antworten](https://language.azure.com/languageStudio/questionAnswering/projects).

3. Wählen Sie **Neues Projekt erstellen** aus.

4. Wenn Sie das erste Projekt erstellen, das Ihrer Sprachressource zugeordnet ist, haben Sie die Möglichkeit, zukünftige Projekte mit mehreren Sprachen für dieselbe Ressource zu erstellen. Wenn Sie die Sprache im ersten Projekt explizit auf eine einzelne Sprache festlegen möchten, können Sie diese Einstellung später nicht mehr ändern, und alle nachfolgenden Projekte für diese Ressource verwenden die Sprache, die bei der Erstellung Ihres ersten Projekts ausgewählt wurde.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Benutzeroberfläche für die Sprachauswahl](../media/manage-knowledge-base/choose-language-option.png)

5. Geben Sie grundlegende Projekteinstellungen ein:

    |Einstellung| Wert|
    |-------|------|
    |**Name** | Geben Sie hier Ihren eindeutigen Projektnamen ein („Projekt“ und „Wissensdatenbank“ beziehen sich in der Fragen und Antworten auf dasselbe.) |
    |**Beschreibung** | Geben Sie eine Beschreibung für Ihr Projekt ein |
    |**Ausgangssprache** | Ob dieser Wert ausgegraut wird, hängt von der Auswahl ab, die getroffen wurde, als das erste mit der Sprachressource verbundene Projekt erstellt wurde.  |
    |**Standardantwort** | Die Standardantwort, die das System sendet, wenn keine Antwort für die Frage gefunden wurde. Sie können dies jederzeit in den Projekteinstellungen ändern.

## <a name="manage-projects"></a>Verwalten von Projekten

Auf der Hauptseite von Fragen und Antworten in Language Studio haben Sie folgende Möglichkeiten:

- Erstellen von Projekten
- Projekte löschen
- Exportieren vorhandener Projekte für die Sicherung oder die Migration zu anderen Sprachressourcen
- Importieren von Projekten/Wissensdatenbanken. (Das erwartete Dateiformat ist eine `.zip` Datei, die ein Projekt/eine Wissensdatenbank enthält, die im Format `excel` oder `.tsv` exportiert wurde.)
- Projekte können entweder nach dem Datum der **letzten Änderung** oder nach dem Datum der **letzten Veröffentlichung** geordnet werden.

## <a name="manage-sources"></a>Verwalten von Quellen

1. Wählen Sie im Navigationsbereich links die Option **Quellen verwalten** aus.

1.  Es gibt drei Arten von Quellen: **URLS**, **Dateien**, und **Chitchat**.

       |Zielsetzung|Aktion|
       |--|--|
       |Quelle hinzufügen|Sie können Ihrem Projekt neue Quellen und FAQ-Inhalte hinzufügen, indem Sie **Quelle hinzufügen** auswählen und **URLs**, **Dateien**, oder **Chitchat** auswählen.|
       |Löschen von Quellen|Sie können vorhandene Quellen löschen, indem Sie links neben der Quelle auswählen. Dadurch wird ein blauer Kreis mit einem Häkchen angezeigt > Wählen Sie das Papierkorbsymbol aus. |
       |Inhalt als unstrukturiert markieren|Wenn Sie den hochgeladenen Dateiinhalt als unstrukturiert markieren möchten, wählen Sie **Unstrukturierter Inhalt** aus der Dropdownliste aus, wenn Sie die Quelle hinzufügen.|
       |Automatische Erkennung| Zulassen von Fragen und Antworten, um zu ermitteln, ob der Inhalt strukturiert oder unstrukturiert ist.|

## <a name="manage-large-projects"></a>Verwalten großer Projekte

Auf der **Seite „Wissensdatenbank bearbeiten“** haben Sie folgende Möglichkeiten:

* **Projekt suchen**: Sie können das Projekt durchsuchen, indem Sie oben im Frageantwortbereich in das Textfeld eingeben. Drücken Sie die Eingabetaste, um in den Fragen, Antworten oder Metadaten danach zu suchen.

* **Paginierung**: Navigieren Sie zur Verwaltung umfangreicher Projekte schnell durch Datenquellen. Seitenzahlen werden am unteren Rand der Benutzeroberfläche angezeigt und befinden sich manchmal außerhalb des Bildschirms.

## <a name="delete-project"></a>Löschen eines Projekts

Das Löschen einer Gruppe ist ein endgültiger Vorgang. Dieser Vorgang kann nicht rückgängig gemacht werden. Vor dem Löschen eines Projekts sollten Sie das Projekt von der Fragen und Antworten-Hauptseite in Language Studio exportieren.

Wenn Sie Ihr Projekt für Projektmitarbeiter freigeben und es später löschen, verliert jeder den Zugriff auf das Projekt.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren der Ressourcen](./configure-resources.md)
