---
title: Neuerungen in der Personalisierung
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Neuigkeiten zur Personalisierung.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/28/2021
ms.openlocfilehash: 4c4d4151342211712f63d254630cc480dd761235
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830237"
---
# <a name="whats-new-in-personalizer"></a>Neuerungen in der Personalisierung

Informieren Sie sich über die Neuerungen im Dienst. Dabei kann es sich um Versionshinweise, Videos, Blogbeiträge und andere Informationen handeln. Legen Sie ein Lesezeichen für diese Seite an, um über den Dienst auf dem Laufenden zu bleiben.

## <a name="release-notes"></a>Versionshinweise

### <a name="may-2021----build-conference"></a>Mai 2021 – //Build-Konferenz

* Automatische Optimierung (Vorschau): Sie können eine Personalisierungsschleife konfigurieren, die Sie verwenden, um sich mit weniger Arbeit im Laufe der Zeit kontinuierlich zu verbessern. Die Personalisierung führt automatisch Offlineauswertungen aus, erkennt bessere Machine Learning-Einstellungen und wendet sie an. Weitere Informationen finden Sie unter [Automatische Optimierung der Personalisierung (Vorschau)](concept-auto-optimization.md).
* Personalisierung mit mehreren Slots (Vorschau): Wenn Sie kachelbasierte Layouts, Karussells und/oder Randleisten haben, ist es einfacher, die Personalisierung für jeden Ort zu verwenden, an dem Sie Produkte oder Inhalte auf der gleichen Seite empfehlen. Die Personalisierung kann jetzt eine Liste von Slots in der Rangfolge-API verwenden, jedem eine Aktion zuweisen und aus Relevanzbewertungen lernen, die Sie für jeden Slot senden. Weitere Informationen finden Sie unter [Multislot-Personalisierung (Vorschau)](concept-multi-slot-personalization.md).
* Die Personalisierung ist jetzt in weiteren Regionen verfügbar.
* Aktualisierte Codebeispiele (GitHub) und Dokumentation. Verwenden Sie die folgenden Links, um aktualisierte Beispiele zu sehen:
  * [C#/.NET](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)
  * [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)
  * [Python-Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

### <a name="july-2020"></a>Juli 2020

* Neues Tutorial: [Verwendung der Personalisierung in einem Chatbot](tutorial-use-personalizer-chat-bot.md)

### <a name="june-2020"></a>Juni 2020

* Neues Tutorial: [Verwendung der Personalisierung in einer Web-App](tutorial-use-personalizer-web-app.md)

### <a name="may-2020---build-conference"></a>Mai 2020 – //Build-Konferenz

Folgendes ist in der **Public Preview** verfügbar:

 * [Ausbildungsmodus](concept-apprentice-mode.md) als Lernverhalten.

### <a name="march-2020"></a>März 2020

* TLS 1.2 wird nun für alle HTTP-Anforderungen erzwungen, die an diesen Dienst gerichtet werden. Weitere Informationen finden Sie unter [Sicherheit von Azure Cognitive Services](../cognitive-services-security.md).

### <a name="november-2019---ignite-conference"></a>November 2019 – Ignite-Konferenz

* Die Personalisierung ist allgemein verfügbar (GA)
* Azure Notebooks-[Tutorial](tutorial-use-azure-notebook-generate-loop-data.md) mit dem gesamten Lebenszyklus

### <a name="may-2019---build-conference"></a>Mai 2019 – //Build-Konferenz

Die folgenden Previewfunktionen wurden bei der Build 2019-Konferenz veröffentlicht:

* [Lernschleife mit Bewertung und Belohnung](what-is-personalizer.md)

## <a name="videos"></a>Videos

### <a name="2019-build-videos"></a>Build 2019 – Videos

* [Mit Cognitive Services Personalizer die richtigen Erfahrungen und Inhalte liefern, etwa für Xbox](https://azure.microsoft.com/resources/videos/build-2019-deliver-the-right-experiences-and-content-with-cognitive-services-personalizer/)

## <a name="service-updates"></a>Dienstupdates

[Azure-Updateankündigungen für Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen einer Feedbackschleife in C#](./quickstart-personalizer-sdk.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Verwenden der interaktiven Demo](https://personalizationdemo.azurewebsites.net/)