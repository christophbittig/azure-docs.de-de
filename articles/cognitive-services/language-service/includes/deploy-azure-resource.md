---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: adbcef246e8b027230ec436de9d9a1da838e523f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095334"
---
Zur Verwendung von Language Studio benötigen Sie eine Azure-Ressource für Sprache für die Authentifizierung. Sie können diese Ressource auch verwenden, um die Feature-REST-APIs und Clientbibliotheken aufzurufen. Führen Sie zum Einstieg die folgenden Schritte aus. 

> [!IMPORTANT] 
> Der Setupprozess und die Anforderungen für die benutzerdefinierten Features unterscheiden sich. Wenn Sie eines der benutzerdefinierten Features verwenden, empfiehlt es sich, einen der unten verlinkten Schnellstartartikel für einen leichteren Einstieg zu lesen.  
> * [Unterhaltungssprachverständnis](../conversational-language-understanding/quickstart.md)
> * [Benutzerdefinierte Textklassifizierung](../custom-classification/quickstart.md)
> * [Benutzerdefinierte Erkennung benannter Entitäten (Custom Named Entity Recognition, NER)](../custom-named-entity-recognition/quickstart.md) 

1. Erstellen Sie ein Azure-Abonnement. Sie können [ein kostenloses Konto erstellen](https://azure.microsoft.com/free/ai/). 

2. [Melden Sie sich bei Language Studio an.](https://aka.ms/languageStudio) Wenn Sie sich zum ersten Mal anmelden, wird ein Fenster angezeigt, in dem Sie eine Sprachressource auswählen können. 

   :::image type="content" source="../media/language-resource-small.png" alt-text="Screenshot: Bildschirm für die Ressourcenauswahl in Language Studio" lightbox="../media/language-resource.png":::

3. Wählen Sie **Create a new language resource** (Neue Sprachressource auswählen) aus. Geben Sie dann Informationen für Ihre neue Ressource ein, z. B. einen Namen, einen Standort und eine Ressourcengruppe.

    
    > [!TIP]
    > * Wählen Sie beim Festlegen eines Standorts für Ihre Azure-Ressource einen Standort in der Nähe aus, um eine geringere Wartezeit zu erzielen.
    > * Es wird empfohlen, die Option **Verwaltete Identität** auf **Ein** festzulegen, um Ihre Anforderungen in Azure zu authentifizieren.
    > * Wenn Sie den Tarif „Free“ verwenden, können Sie den Sprachdienst auch nach Ablauf Ihrer kostenlosen Azure-Testversion oder Ihres Dienstguthabens weiterhin nutzen. 

    :::image type="content" source="../media/create-new-resource-small.png" alt-text="Screenshot: Bildschirm zum Erstellen von Ressourcen in Language Studio." lightbox="../media/create-new-resource.png":::

4. Wählen Sie **Fertig** aus. Ihre Ressource wird erstellt, und Sie können die verschiedenen Features des Sprachdiensts ausprobieren. Wählen Sie beispielsweise **Verknüpfte Entitäten suchen** aus.

    :::image type="content" source="../media/language-studio-main-screen.png" alt-text="Screenshot: Hauptbildschirm in Language Studio" lightbox="../media/language-studio-main-screen.png":::


5. Dieses Feature enthält einen Abschnitt zum Eingeben von Text, Hochladen einer Datei oder Auswählen eines Textbeispiels, um die Funktionsweise des Features zu veranschaulichen. Um die Demo auszuprobieren, müssen Sie eine Ressource auswählen und bestätigen. Dadurch wird eine Nutzung gemäß Ihres [Tarifs](https://aka.ms/unifiedLanguagePricing) verursacht.

    :::image type="content" source="../media/language-studio-feature.png" alt-text="Screenshot: Feature in Language Studio" lightbox="../media/language-studio-feature.png":::

6. Nach dem Senden von Text wird eine Visualisierung zusammen mit der JSON-Antwort angezeigt. Am unteren Rand der Seite werden die nächsten Schritte und der cURL-Befehl für die soeben gesendete API-Anforderung angezeigt.

    :::image type="content" source="../media/language-studio-feature-result.png" alt-text="Screenshot: Ergebnis eines Features in Language Studio" lightbox="../media/language-studio-feature-result.png":::
