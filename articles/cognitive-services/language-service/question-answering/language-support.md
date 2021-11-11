---
title: 'Sprachunterstützung : Benutzerdefinierte Fragebeantwortung'
titleSuffix: Azure Cognitive Services
description: Eine Liste der Kulturen und natürliche Sprachen, die von der benutzerdefinierten Fragebeantwortung für Ihre Wissensdatenbank unterstützt werden. Mischen Sie Sprachen nicht in derselben Wissensdatenbank.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: reference
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: dbef13bdb39085c650a1fc5cedceb6c143eea4ad
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450113"
---
# <a name="language-support-for-custom-question-answering-and-knowledge-bases"></a>Sprachunterstützung für benutzerdefinierte Fragebeantwortung und Wissensdatenbanken

In diesem Artikel werden die Sprachunterstützungsoptionen für Ressourcen und Wissensdatenbanken mit aktivierter benutzerdefinierter Fragebeantwortung beschrieben.

Bei der benutzerdefinierten Fragebeantwortung haben Sie die Möglichkeit, die Sprache jedes Mal beim Hinzufügen eines neuen Projekts zu einer Ressource auszuwählen, das die Unterstützung mehrerer Sprachen zulässt, oder Sie können eine Sprache auswählen, die für alle zukünftigen Projekte für eine Ressource gilt.

## <a name="supporting-multiple-languages-in-one-custom-question-answering-enabled-resource"></a>Unterstützen mehrerer Sprachen in einer Ressource mit aktivierter benutzerdefinierter Fragebeantwortung

> [!div class="mx-imgBorder"]
> ![Auswahl einer mehrsprachigen Wissensdatenbank](./media/language-support/choose-language.png)

* Wenn Sie das erste Projekt in Ihrem Dienst erstellen, können Sie bei jeder Erstellung eines neuen Projekts die Sprache auswählen. Aktivieren Sie diese Option, um Wissensdatenbanken, die zu verschiedenen Sprachen gehören, in einem Dienst zu erstellen.
* Die Spracheinstellungsoption kann für den Dienst nicht mehr geändert werden, sobald die erste Wissensdatenbank erstellt wurde.
* Wenn Sie mehrere Sprachen für die Wissensdatenbank aktivieren, haben Sie statt eines Testindex für den Dienst einen Testindex pro Wissensdatenbank.

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Unterstützung mehrerer Sprachen in einer Wissensdatenbank

Wenn Sie ein Wissensdatenbank-System unterstützen müssen, das mehrere Sprachen umfasst, stehen die folgenden Methoden zur Wahl:

* Verwenden Sie den [Translator-Diensts](../../translator/translator-info-overview.md), um eine Frage in eine einzelne Sprache zu übersetzen, bevor die Frage an Ihre Wissensdatenbank gesendet wird. Dies erlaubt es Ihnen, sich auf die Qualität einer einzelnen Sprache und die Qualität der alternativen Fragen und Antworten zu konzentrieren.
* Erstellen Sie für jede Sprache eine Sprachressource mit aktivierter benutzerdefinierter Fragebeantwortung und eine Wissensdatenbank innerhalb dieser Ressource. Auf diese Weise können Sie für jede Sprache separate alternative Frage- und Antworttexte mit stärkerer Nuancierung verwenden. Dadurch erhalten Sie eine viel größere Flexibilität, allerdings um den Preis eines deutlich höheren Verwaltungsaufwands, wenn sich die Fragen oder Antworten über die Sprachen hinweg unterscheiden.

## <a name="single-language-per-resource"></a>Einzelne Sprache pro Ressource

Wenn Sie **die Option zum Festlegen der Sprache auswählen, die von allen mit der Ressource verknüpften Projekten verwendet wird**, beachten Sie Folgendes: 
* Eine Sprachressource und alle zugehörigen Projekte/Wissensdatenbanken unterstützen nur eine Sprache.
* Die Sprache wird explizit festgelegt, wenn das erste Projekt des Diensts erstellt wird.
* Die Sprache kann für andere Projekte, die der Ressource zugeordnet sind, nicht geändert werden.
* Die Sprache wird vom Cognitive Search-Dienst (Bewerter 1) und von „Benutzerdefinierte Fragen und Antworten“ (Bewerter 2) verwendet, um die beste Antwort auf eine Abfrage zu generieren.

## <a name="languages-supported"></a>Unterstützte Sprachen

Die folgende Liste enthält die Sprachen, die für Fragen und Antworten-Ressourcen unterstützt werden.

| Sprache |
|--|
| Arabisch |
| Armenisch |
| Bengalisch |
| Baskisch |
| Bulgarisch |
| Katalanisch |
| Chinesisch (vereinfacht) |
| Chinesisch (traditionell) |
| Kroatisch |
| Tschechisch |
| Dänisch |
| Niederländisch |
| Englisch |
| Estnisch |
| Finnisch |
| Französisch |
| Galizisch |
| Deutsch |
| Griechisch |
| Gujarati |
| Hebräisch |
| Hindi |
| Ungarisch |
| Isländisch |
| Indonesisch |
| Irisch |
| Italienisch |
| Japanisch |
| Kannada |
| Koreanisch |
| Lettisch |
| Litauisch |
| Malayalam |
| Malaiisch |
| Norwegisch |
| Polnisch |
| Portugiesisch |
| Pandschabi |
| Rumänisch |
| Russisch |
| Serbisch (Kyrillisch) |
| Serbisch (Lateinisch) |
| Slowakisch |
| Slowenisch |
| Spanisch |
| Schwedisch |
| Tamilisch |
| Telugu |
| Thailändisch |
| Türkisch |
| Ukrainisch |
| Urdu |
| Vietnamesisch |

## <a name="query-matching-and-relevance"></a>Abfrageabgleich und Relevanz
Die benutzerdefinierte Fragebeantwortung nutzt [Sprachanalysefunktionen von Azure Cognitive Search](/rest/api/searchservice/language-support), um Ergebnisse bereitzustellen.

Während die Azure Cognitive Search-Funktionen für unterstützte Sprachen ebenbürtig sind, verfügt die Fragebeantwortung über ein zusätzliches Rangfolgemodul, das oberhalb der Azure-Suchergebnisse ansetzt. In diesem Rangfolgemodul verwenden wir einige besondere semantische und wortbasierte Funktionen in den folgenden Sprachen.

|Sprachen mit zusätzlichem Rangfolgemodul|
|--|
|Chinesisch|
|Tschechisch|
|Niederländisch|
|Englisch|
|Französisch|
|Deutsch|
|Ungarisch|
|Italienisch|
|Japanisch|
|Koreanisch|
|Polnisch|
|Portugiesisch|
|Spanisch|
|Schwedisch|

Diese zusätzliche Rangfolge ist eine interne Funktion des Bewerters der benutzerdefinierten Fragebeantwortung.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Sprachauswahl](../index.yml)
