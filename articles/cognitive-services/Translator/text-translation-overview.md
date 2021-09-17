---
title: Was ist Microsoft Azure Cognitive Services-Textübersetzung?
titlesuffix: Azure Cognitive Services
description: Integrieren Sie die Textübersetzungs-API in Ihre Anwendungen, Websites, Tools und in andere Lösungen, um Benutzererlebnisse in mehreren Sprachen bereitzustellen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/09/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Übersetzung, Textübersetzung, maschinelle Übersetzung, Übersetzungsdienst, benutzerdefinierte Übersetzung
ms.openlocfilehash: 68354706935d0dcfb29055a1eb5b0e273022d306
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785228"
---
# <a name="what-is-text-translation"></a>Was ist „Textübersetzung“?

 Bei der Textübersetzung handelt es sich um ein cloudbasiertes REST-API-Feature des Übersetzungsdiensts, das die Technologie der neuronalen maschinellen Übersetzung verwendet, um eine schnelle und genaue Übersetzung von Ausgangstext zu Zieltext in Echtzeit in allen [unterstützten Sprachen](language-support.md) zu ermöglichen. In dieser Übersicht erfahren Sie, wie Sie mit den REST-APIs für die Textübersetzung intelligente Lösungen für Ihre Anwendungen und Workflows erstellen können.

Die Dokumentation zur Textübersetzung enthält die folgenden Arten von Artikeln:

* [**Schnellstarts**](quickstart-translator.md). Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](translator-how-to-signup.md). Anweisungen für den Zugriff auf den Dienst und zur spezifischeren oder individuelleren Verwendung des Diensts.
* [**Referenzartikel**](reference/v3-0-reference.md). REST-API-Dokumentation und von der Programmiersprache abhängige Inhalte.

## <a name="text-translation-features"></a>Features für die Textübersetzung

 Die folgenden Methoden werden vom Feature für die Textübersetzung unterstützt:

* [**Sprachen**](reference/v3-0-languages.md). Gibt eine Liste der Sprachen zurück, die von den Vorgängen **Übersetzen**, **Transliterieren** und **Wörterbuchsuche** unterstützt werden. Für diese Anforderung ist keine Authentifizierung erforderlich. Kopieren Sie einfach die folgende GET-Anforderung, und fügen Sie sie in Postman oder Ihr bevorzugtes API-Tool oder Ihren Browser ein:

    ```http
    https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
    ```

* [**Übersetzen**](reference/v3-0-translate.md#translate-to-multiple-languages). Rendert Text in einer einzelnen Quellsprache mit einer einzelnen Anforderung in Texte in mehreren Zielsprachen.

* [**Transliterieren**](reference/v3-0-transliterate.md). Konvertiert Zeichen oder Buchstaben einer Quellsprache in die entsprechenden Zeichen oder Buchstaben einer Zielsprache.

* [**Erkennen**](reference/v3-0-detect.md). Gibt den Sprachcode des Quellcodes und eine boolesche Variable zurück, die angibt, ob die erkannte Sprache für die Textübersetzung und Transliteration unterstützt wird.

    > [!NOTE]
    > Sie können Text mit [einem einzelnen REST-API-Aufruf](reference/v3-0-translate.md#translate-a-single-input-with-language-autodetection) **übersetzen, transliterieren und erkennen**.

* [**Wörterbuchsuche**](reference/v3-0-dictionary-lookup.md). Gibt äquivalente Wörter für den Quellbegriff in der Zielsprache zurück.
* [**Wörterbuchbeispiel**](reference/v3-0-dictionary-examples.md). Gibt die grammatikalische Struktur und Kontextbeispiele für das Paar aus Quell- und Zielbegriff zurück.

## <a name="text-translation-deployment-options"></a>Bereitstellungsoptionen für die Textübersetzung

Fügen Sie Ihren Projekten und Anwendungen mithilfe der folgenden Ressourcen die Textübersetzung hinzu:

* Greifen Sie über die [**REST-API**](reference/rest-api-guide.md) auf den cloudbasierten Textübersetzungsdienst zu, der in Azure verfügbar ist.

* Verwenden Sie die [Übersetzungsanforderung](containers/translator-container-supported-parameters.md) der REST-API mit dem [**Docker-Container für die Textübersetzung**](containers/translator-how-to-install-container.md).

    > [!IMPORTANT]
    >
    > * Der Textübersetzungscontainer befindet sich in der geschlossenen Vorschauphase. Um ihn zu verwenden, müssen Sie das Onlineanforderungsformular für [**Azure Cognitive Services Application für geschlossene Dienste**](https://aka.ms/csgate-translator) ausfüllen und übermitteln und zudem genehmigen lassen, um Zugriff auf den Container zu erhalten.
    >
    > * Das [**Containerimage für die Textübersetzung**](https://hub.docker.com/_/microsoft-azure-cognitive-services-translator-text-translation) unterstützt im Vergleich zu den Cloudangeboten eingeschränkte Features.
    >

## <a name="get-started-with-text-translation"></a>Erste Schritte bei der Textübersetzung

Bereit für den Einstieg?

* [**Erstellen Sie eine Übersetzerressource**](translator-how-to-signup.md "Öffnen Sie das Azure-Portal.") im Azure-Portal.

* [**Rufen Sie Ihre Zugriffsschlüssel und den API-Endpunkt ab**](translator-how-to-signup.md#authentication-keys-and-endpoint-url). Für die Authentifizierung sind eine Endpunkt-URL und ein schreibgeschützter Schlüssel erforderlich.

* Erkunden Sie unsere [**Schnellstartanleitung**](quickstart-translator.md "Erfahren Sie, wie Sie die Textübersetzung über REST und eine bevorzugte Programmiersprache verwenden."), und zeigen Sie Anwendungsfälle und Codebeispiele für die folgenden Programmiersprachen an: 
  * [**C#/.NET**](quickstart-translator.md?tabs=csharp)
  * [**Go**](quickstart-translator.md?tabs=go)
  * [**Java**](quickstart-translator.md?tabs=java)
  * [**JavaScript/Node.js**](quickstart-translator.md?tabs=nodejs)
  * [**Python**](quickstart-translator.md?tabs=python)

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Informationen zur Textübersetzungs-REST-API:

> [!div class="nextstepaction"]
> Weitere Informationen finden Sie in der [REST-API-Referenz](./reference/v3-0-reference.md).
