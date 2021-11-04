---
title: Erstellen von Projekten in Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Projekte in Conversational Language Understanding erstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c837912ea60b0caf91e2d04382af343bd4b6a96a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101358"
---
# <a name="how-to-create-projects-in-conversational-language-understanding"></a>Erstellen von Projekten in Conversational Language Understanding

Conversational Language Understanding ermöglicht Ihnen das Erstellen von zwei Typen von Projekten: **Unterhaltungs**- und **Orchestrierungsworkflow**-Projekte.

## <a name="sign-in-to-language-studio"></a>Anmelden bei Language Studio
Zunächst müssen Sie sich bei [Language Studio](https://aka.ms/languageStudio) anmelden und eine Sprachressource erstellen. Wählen Sie **Fertig** aus, sobald die Auswahl abgeschlossen ist.

## <a name="navigate-to-conversational-language-understanding"></a>Navigieren Sie zu Conversational Language Understanding

Suchen Sie in Language Studio den Abschnitt **Understand conversational language** (Konversationssprache verstehen), und wählen Sie **Conversational language understanding** (Language Understanding für Unterhaltungen) aus.

Die Projektseite von Conversational Language Understanding wird angezeigt.

:::image type="content" source="../media/projects-page.png" alt-text="Screenshot der Projektseite von Conversational Language Understanding." lightbox="../media/projects-page.png":::

## <a name="create-a-conversation-project"></a>Erstellen eines Unterhaltungsprojekts
Nach dem Auswählen von Unterhaltung müssen Sie die folgenden Details angeben:
- Name: Projektname
- Beschreibung: optionale Projektbeschreibung
- Hauptsprache des Texts: Die Hauptsprache Ihres Projekts. Ihre Trainingsdaten sollten hauptsächlich in dieser Sprache vorliegen.
- Mehrere Sprachen aktivieren: Gibt an, ob Sie ihrem Projekt die gleichzeitige Unterstützung mehrerer Sprachen ermöglichen möchten.

:::image type="content" source="../media/clu-project-modal.png" alt-text="Screenshot: Projekterstellungsfenster für Unterhaltungen in Conversational Language Understanding." lightbox="../media/clu-project-modal.png":::

Nachdem Sie damit fertig sind, klicken Sie auf „Weiter“, überprüfen die Details und klicken dann auf „Projekt erstellen“, um den Vorgang abzuschließen. 

## <a name="create-an-orchestration-workflow-project"></a>Erstellen eines Orchestrierungsworkflow-Projekts

Nach dem Auswählen von Orchestrierung müssen Sie die folgenden Details angeben:
- Name: Projektname
- Beschreibung: optionale Projektbeschreibung
- Hauptsprache des Texts: Die Hauptsprache Ihres Projekts. Ihre Trainingsdaten sollten hauptsächlich in dieser Sprache vorliegen.
- Mehrere Sprachen aktivieren: Gibt an, ob Sie ihrem Projekt die gleichzeitige Unterstützung mehrerer Sprachen ermöglichen möchten.

Sobald Sie fertig sind, haben Sie jetzt die Möglichkeit, eine Verbindung mit den anderen Projekten und Diensten herzustellen, die in die Orchestrierung eingezogen werden sollen. Jede Verbindung wird durch ihren Typ und relevante Daten dargestellt. Die Absicht muss einen **Namen** und einen **Projekttyp** (LUIS, Antworten auf benutzerdefinierte Fragen (Custom Question Answering, QnA) oder Conversational Language Understanding) aufweisen. Anschließend wählen Sie das Projekt, mit dem eine Verbindung hergestellt werden soll, anhand des Namens aus. 

> [!NOTE]
> Die Liste der Projekte, mit denen Sie eine Verbindung herstellen können, sind nur Projekte, die sich im Besitz derselben Sprachressource befinden, die Sie zum Erstellen des Orchestrierungsprojekts verwenden.

Dieser Schritt ist optional, und Sie haben nach dem Erstellen des Projekts weiterhin die Möglichkeit, Absichtsverbindungen hinzuzufügen.

:::image type="content" source="../media/orchestration-project-detail.png" alt-text="Screenshot: Modales Dialogfeld des Orchestrierungsworkflow-Projekts „Conversational Language Understanding“." lightbox="../media/orchestration-project-detail.png":::

## <a name="import-a-project"></a>Importieren eines Projekts

Sie können ein Conversational Language Understanding-Projekt jederzeit als JSON-Datei exportieren, indem Sie zur Seite mit den Unterhaltungsprojekten wechseln, ein Projekt auswählen und auf **Exportieren** drücken.
Dieses Projekt kann als neues Projekt erneut importiert werden. Wenn Sie ein Projekt mit genau demselben Namen importieren, werden die Daten des Projekts durch die Daten des neu importierten Projekts ersetzt.

:::image type="content" source="../media/export.png" alt-text="Screenshot der Schaltfläche „Exportieren“ von Conversational Language Understanding." lightbox="../media/export.png":::

Wenn Sie über eine vorhandene LUIS-Anwendung verfügen, können Sie den JSON-Code der LUIS-Anwendung direkt in Conversational Language Understanding _importieren_, dann wird ein Unterhaltungsprojekt mit allen aktuell verfügbaren Teilen erstellt: Absichten, ML-Entitäten und Äußerungen. Weitere Informationen finden Sie unter [Abwärtskompatibilität mit LUIS](../concepts/backwards-compatibility.md).

Klicken Sie auf die Pfeilschaltfläche neben **Neues Projekt erstellen**, wählen Sie **Importieren** aus, und wählen Sie dann die JSON-Datei von LUIS oder Conversational Language Understanding aus.

:::image type="content" source="../media/import.png" alt-text="Screenshot der Schaltfläche „Importieren“ von Conversational Language Understanding." lightbox="../media/import.png":::

## <a name="next-steps"></a>Nächste Schritte

[Schemaerstellung](./build-schema.md)
