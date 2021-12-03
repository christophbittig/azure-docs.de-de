---
title: Kennzeichen von Äußerungen in Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Artikel, um Ihre Äußerungen in Conversational Language Understanding-Projekten zu kennzeichnen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 73ab3df8a7f13ec2c734206772f55bee626b871a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339932"
---
# <a name="how-to-tag-utterances"></a>Kennzeichnen von Äußerungen

Nachdem Sie für Ihr Projekt [ein Schema erstellt](build-schema.md) haben, sollten Sie Ihrem Projekt Trainingsäußerungen hinzufügen. Die Äußerungen sollten denen ähnlich sein, die Ihre Benutzer bei der Interaktion mit dem Projekt verwenden. Wenn Sie eine Äußerung hinzufügen, müssen Sie zuweisen, zu welcher Absicht sie gehört. Nachdem die Äußerung hinzugefügt wurde, beschriften Sie die Wörter in Ihrer Äußerung mit den Entitäten in Ihrem Projekt. Ihre Bezeichnungen für Entitäten sollten über die verschiedenen Äußerungen hinweg konsistent sein.

Kennzeichnen ist der Prozess, bei dem Ihre Äußerungen Absichten zugewiesen und mit Entitäten bezeichnet werden. Sie sollten sich Zeit für das Kennzeichnen Ihrer Äußerungen nehmen – für das Einführen und Optimieren der Daten, mit denen die zugrunde liegenden Machine Learning-Modelle für Ihr Projekt trainiert werden. Die Machine Learning-Modelle verallgemeinern auf der Grundlage der Beispiele, die Sie bereitstellen. Je mehr Beispiele Sie angeben, desto mehr Datenpunkte stehen dem Modell zur Verfügung, um bessere Verallgemeinerungen abzuleiten.

> [!NOTE]
>  Die gelernten Komponenten einer Entität werden nur definiert, wenn Sie Äußerungen für diese Entität bezeichnen. Sie können auch Entitäten verwenden, die _nur_ Listen oder vordefinierte Komponenten beinhalten, ohne gelernte Komponenten zu bezeichnen. Weitere Informationen finden Sie im Artikel zu [Entitätskomponenten](../concepts/entity-components.md).

Wenn Sie in Ihrem Projekt mehrere Sprachen aktivieren, müssen Sie außerdem die Sprache der Äußerung angeben, die Sie hinzufügen. Im Rahmen der Mehrsprachenfähigkeit von Conversational Language Understanding können Sie Ihr Projekt in einer dominanten Sprache trainieren und dann Vorhersagen in den anderen verfügbaren Sprachen treffen. Durch das Hinzufügen von Beispielen zu anderen Sprachen wird die Leistung des Modells in diesen Sprachen verbessert, sollten Sie feststellen, dass es nicht gut funktioniert. Vermeiden Sie es aber, Ihre Daten über alle Sprachen, die Sie unterstützen möchten, zu verdoppeln. 

Zum Verbessern der Leistung eines Kalenderbots mit Benutzern könnte ein Entwickler beispielsweise vorwiegend englischsprachige Beispiele hinzufügen und darüber hinaus einige weitere in Spanisch oder Französisch. Er könnte etwa Äußerungen wie diese hinzufügen:

* „_Buche eine Besprechung mit **Matt** und **Kevin** **Morgen**  um **12:00 Uhr**._ “ (Englisch)
* „_Antworte **mit Vorbehalt** auf die **wöchentliche Update**-Besprechung._ “ (Englisch)
* „_Cancelar mi **próxima** reunión_.“ (Spanisch)

In Orchestrierungsworkflow-Projekten werden die Daten, die zum Trainieren verbundener Absichten verwendet werden, nicht innerhalb des Projekts bereitgestellt. Stattdessen ruft das Projekt die Daten während des Trainings als Pull aus dem verbundenen Dienst ab (z. B. verbundene LUIS-Anwendungen, Conversational Language Understanding-Projekte oder Wissensdatenbanken zur Beantwortung von benutzerdefinierten Fragen). Wenn Sie jedoch Absichten erstellen, die nicht mit einem Dienst verbunden sind, müssen Sie diesen Absichten trotzdem Äußerungen hinzufügen.

Beispielsweise kann ein Entwickler eine Absicht für jede seiner Qualifikationen erstellen und sie mit einem entsprechenden Kalenderprojekt, einem E-Mail-Projekt und einer FAQ-Wissensdatenbank des Unternehmens verbinden. 

## <a name="tag-utterances"></a>Kennzeichnen von Äußerungen

:::image type="content" source="../media/tag-utterances.png" alt-text="Screenshot der Seite zum Kennzeichnen von Äußerungen in Language Studio." lightbox="../media/tag-utterances.png":::

*Abschnitt 1* ist der Ort, an dem Sie Ihre Äußerungen hinzufügen. Sie müssen eine der Absichten aus der Dropdownliste auswählen, die Sprache der Äußerung (falls zutreffend) und die Äußerung selbst. Drücken Sie die *EINGABETASTE* im Textfeld der Äußerung, um sie hinzuzufügen.

*Abschnitt 2* enthält die Entitäten Ihres Projekts. Sie können eine der von Ihnen hinzugefügten Entitäten auswählen und mit dem Mauszeiger auf den Text zeigen, um die Entitäten innerhalb Ihrer Äußerungen zu kennzeichnen, wie in *Abschnitt 3* gezeigt. Sie können hier auch neue Entitäten hinzufügen, indem Sie auf die Schaltfläche **+ Entität hinzufügen** klicken. Sie können die Bezeichnungen dieser Entität in Ihren Äußerungen auch ausblenden. 

*Abschnitt 3* enthält die Äußerungen, die Sie hinzugefügt haben. Sie können mit dem Mauszeiger über den Text ziehen, den Sie kennzeichnen möchten, dann wird ein Kontextmenü der Entitäten angezeigt.

:::image type="content" source="../media/label-utterance-menu.png" alt-text="Beispiel zum Auswählen von Text, um eine Bezeichnung hinzuzufügen." lightbox="../media/label-utterance-menu.png":::

> [!NOTE]
> Im Gegensatz zu LUIS können Sie keine Bezeichnungen für überlappende Entitäten angeben. Die gleichen Zeichen können nicht von mehr als einer Entität bezeichnet werden.

## <a name="filter-utterances"></a>Filtern von Äußerungen

Durch Klicken auf **Filter** können Sie nur die Äußerungen anzeigen, die den im Filterbereich ausgewählten Absichten und Entitäten zugeordnet sind.
Wenn Sie auf der Seite [Schema erstellen](./build-schema.md) auf eine Absicht klicken, gelangen Sie zur Seite **Äußerungen kennzeichnen**, die automatisch nach der betreffenden Absicht gefiltert ist. 

## <a name="next-steps"></a>Nächste Schritte
* [Trainieren und Bewerten eines Modells](./train-model.md)
