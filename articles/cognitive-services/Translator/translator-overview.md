---
title: Was ist der Microsoft Azure Cognitive Services-Übersetzer?
titlesuffix: Azure Cognitive Services
description: Integrieren Sie Translator in Ihre Anwendungen, Websites, Tools und in andere Lösungen, um Benutzererlebnisse in mehreren Sprachen bereitzustellen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/10/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Übersetzung, Textübersetzung, maschinelle Übersetzung, Übersetzungsdienst, benutzerdefinierte Übersetzung
ms.openlocfilehash: 4d5e60beeb69447823369a5dfe610e73354f1ddc
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426956"
---
# <a name="what-is-translator"></a>Worum handelt es sich bei Translator?

 Der Übersetzer ist ein cloudbasierter Dienst für neuronale maschinelle Übersetzung, der zur [Azure Cognitive Services](../what-are-cognitive-services.md)-Familie der REST-APIs gehört.  Der Übersetzer kann mit jedem Betriebssystem verwendet werden und unterstützt viele Microsoft-Produkte und -Dienste, die von Tausenden von Unternehmen weltweit verwendet werden, um Sprachübersetzungen und andere sprachbezogene Vorgänge durchzuführen. In dieser Übersicht erfahren Sie, wie Sie mit dem Übersetzer intelligente, mehrsprachige Lösungen für Ihre Anwendungen in allen [unterstützten Sprachen](./language-support.md) erstellen können.

Die Dokumentation zum Übersetzer enthält die folgenden Arten von Artikeln:

* [**Schnellstarts**](quickstart-translator.md). Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](translator-how-to-signup.md). Anweisungen für den Zugriff auf den Dienst und zur spezifischeren oder individuelleren Verwendung des Diensts.
* [**Referenzartikel**](reference/v3-0-reference.md). REST-API-Dokumentation und von der Programmiersprache abhängige Inhalte.

## <a name="translator-features-and-development-options"></a>Features und Entwicklungsoptionen des Übersetzers

Die folgenden Features werden vom Übersetzerdienst unterstützt. Verwenden Sie die Links in dieser Tabelle, um mehr über die einzelnen Feature zu erfahren und die API-Referenzen zu durchsuchen.

| Funktion | BESCHREIBUNG | Entwicklungsoptionen |
|----------|-------------|--------------------------|
| [**Textübersetzung**](text-translation-overview.md) | Führen Sie Textübersetzungen zwischen unterstützten Quell- und Zielsprachen in Echtzeit aus. | <ul><li>[**REST-API**](reference/rest-api-guide.md) </li><li>[Docker-Container für die Textübersetzung](containers/translator-how-to-install-container.md): Befindet sich derzeit in der geschlossenen Vorschauphase.</li></ul> |
| [**Dokumentübersetzung**](document-translation/overview.md) | Übersetzen Sie Batchdateien und komplexe Dateien unter Beibehaltung der Struktur und des Formats der Originaldokumente. | <ul><li>[**REST-API**](document-translation/reference/rest-api-guide.md)</li><li>[**Clientbibliothek/SDK**](document-translation/client-sdks.md)</li></ul> |
| [**Benutzerdefinierter Translator**](custom-translator/overview.md) | Erstellen Sie angepasste Modelle, um domänen- und branchenspezifische Sprache, Terminologie und Stile zu übersetzen. | <ul><li>[**Portal für den benutzerdefinierten Translator**](https://portal.customtranslator.azure.ai/)</li></ul> |

## <a name="try-the-translator-service-for-free"></a>Testen Sie den Übersetzerdienst kostenlos.

Zunächst benötigen Sie ein Microsoft-Konto. Wenn Sie keines besitzen, können Sie sich kostenlos beim [**Microsoft-Kontoportal**](https://account.microsoft.com/account) registrieren.  Wählen Sie **Microsoft-Konto erstellen** aus, und führen Sie die Schritte zum Erstellen und Überprüfen Ihres neuen Kontos aus.

Als nächstes benötigen Sie ein Azure-Konto. Rufen Sie die [**Azure-Anmeldeseite**](https://azure.microsoft.com/free/ai/) auf, wählen Sie die Schaltfläche **Kostenlos starten** aus, und erstellen Sie mithilfe Ihrer Anmeldeinformationen für das Microsoft-Konto ein neues Azure-Konto.

Jetzt können Sie loslegen! [**Erstellen Sie einen Übersetzerdienst**](translator-how-to-signup.md "Öffnen Sie das Azure-Portal."), [**rufen Sie Zugriffsschlüssel und API-Endpunkt ab**](translator-how-to-signup.md#authentication-keys-and-endpoint-url "Für die Authentifizierung sind eine Endpunkt-URL und ein schreibgeschützter Schlüssel erforderlich."), und testen Sie unseren [**Schnellstart**](quickstart-translator.md "Erfahren Sie, wie Sie den Übersetzer mithilfe von REST verwenden.").

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die folgenden Features:
  * [**Textübersetzung**](text-translation-overview.md)
  * [**Dokumentübersetzung**](document-translation/overview.md)
  * [**Benutzerdefinierter Translator**](custom-translator/overview.md)
* Lesen Sie die [**Preisübersicht für den Übersetzer**](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).
