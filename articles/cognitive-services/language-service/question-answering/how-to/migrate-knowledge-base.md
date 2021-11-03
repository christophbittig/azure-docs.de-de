---
title: Migrieren von Projekten und Wissensdatenbanken – benutzerdefinierte Fragen und Antworten
description: Zum Migrieren eines benutzerdefinierten Frageantwortprojekts muss ein Projekt aus einer Ressource exportiert und dann in eine andere importiert werden.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 46c092fa096c6f68299f6e0ac8254bfd25c7ce75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095611"
---
# <a name="migrate-projects-and-question-answer-sources"></a>Migrieren von Projekten und Frageantwortquellen

Sie sollten aus den folgenden Gründen eine Kopie Ihres Projekts erstellen:

* Implementierung eines Sicherungs- und Wiederherstellungsprozesses
* Integration in Ihre CI/CD-Pipeline
* Verschiebung Ihrer Daten in verschiedene Regionen

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.
* Die [Sprachressource](https://aka.ms/create-language-resource) mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“ im Azure-Portal Merken Sie sich die Azure Active Directory-ID, das Abonnement und den Sprachressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

## <a name="export-a-project"></a>Exportieren eines Projekts

Durch das Exportieren eines Projekts können Sie alle Frageantwortquellen migrieren oder sichern, die in einem einzelnen Projekt enthalten sind.

1. Anmelden bei [Language Studio](https://language.azure.com/)
1. Wählen Sie die Sprachressource aus, aus der Sie ein Projekt migrieren möchten.
1. Auf der Seite **Projekte** können Sie in zwei Formate exportieren, Excel oder TSV. Damit wird der Inhalt der Datei bestimmt. Die Datei selbst wird als ZIP-Datei exportiert, die alle Ihre Wissensdatenbanken enthält.

## <a name="import-a-project"></a>Importieren eines Projekts  

1. Wählen Sie die Sprachressource aus, die das Ziel für das zuvor exportierte Projekt ist.
1. Wählen Sie auf der Seite **Projekte** die Option **Importieren** aus, und wählen Sie das Format aus, das beim Auswählen des Exports verwendet wurde. Navigieren Sie dann zur lokalen ZIP-Datei, die das exportierte Projekt enthält. Geben Sie einen Namen für Ihr Projekt ein, und wählen Sie **Fertig** aus.

## <a name="export-question-and-answers"></a>Exportieren von Fragen und Antworten

1. Wählen Sie die Sprachressource aus, aus der Sie eine einzelne Frageantwortquelle migrieren möchten.
1. Wählen Sie das Projekt aus, das die Frageantwortquelle enthält, die Sie exportieren möchten.
1. Wählen Sie auf der Seite „Wissensdatenbank bearbeiten“ die Auslassungspunkte (`...`) rechts neben **Rich-Text aktivieren** auf der Symbolleiste aus. Sie haben die Möglichkeit, in Excel oder TSV zu exportieren.

## <a name="import-question-and-answers"></a>Importieren von Fragen und Antworten

1. Wählen Sie die Sprachressource aus, die das Ziel für die zuvor exportierte Frageantwortquelle ist.
1. Wählen Sie das Projekt aus, in das Sie eine Frageantwortquelle importieren möchten.
1. Wählen Sie auf der Seite „Wissensdatenbank bearbeiten“ die Auslassungspunkte (`...`) rechts neben **Rich-Text aktivieren** auf der Symbolleiste aus. Sie haben die Möglichkeit, eine Excel- oder TSV-Datei zu importieren.
1. Navigieren Sie mit der Option **Datei auswählen** zum lokalen Speicherort der Datei, und wählen Sie **Fertig** aus.

<!-- TODO: Replace Link-->
### <a name="test"></a>Test

**Testen Sie** die Frageantwortquelle, indem Sie auf der Seite **Wissensdatenbank bearbeiten** auf der Symbolleiste die Option **Test** auswählen, um den Testbereich zu starten. Erfahren Sie mehr über das [Testen Ihrer Knowledge Base](../../../qnamaker/How-To/test-knowledge-base.md).

### <a name="deploy"></a>Bereitstellen

<!-- TODO: Replace Link-->
**Stellen Sie die Wissensdatenbank bereit**, und erstellen Sie einen Chatbot. Hier erfahren Sie mehr über das [Bereitstellen Ihrer Wissensdatenbank](../../../qnamaker/Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs"></a>Chatprotokolle

Es gibt keine Möglichkeit, Chatprotokolle mit Projekten oder Wissensdatenbanken zu migrieren. Wenn Diagnoseprotokolle aktiviert sind, werden Chatprotokolle in der zugeordneten Azure Monitor Ressource gespeichert.

## <a name="next-steps"></a>Nächste Schritte

<!-- TODO: Replace Link-->
> [!div class="nextstepaction"]
> [Bearbeiten einer Knowledge Base](../../../qnamaker/How-To/edit-knowledge-base.md)
