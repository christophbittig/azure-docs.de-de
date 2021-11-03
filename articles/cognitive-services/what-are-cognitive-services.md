---
title: Was ist Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Mit Cognitive Services kann jeder Entwickler KI nutzen, ohne mit Machine Learning und Data Science vertraut sein zu müssen. Alles, was Sie brauchen, ist ein API-Aufruf in Ihrer Anwendung, um ihren Apps Funktionen zum Sehen (erweiterte Bildsuche und -erkennung), Hören, Sprechen und Suchen sowie für die Entscheidungsfindung hinzuzufügen.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: Cognitive Services, kognitive Intelligenz, kognitive Lösungen, KI-Dienste, kognitives Verständnis, kognitive Features
ms.service: cognitive-services
ms.topic: overview
ms.date: 10/08/2021
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.openlocfilehash: e19c16d8ee1142374461fb0fdc95c59c84ef8b36
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073318"
---
# <a name="what-are-azure-cognitive-services"></a>Was ist Azure Cognitive Services?

Azure Cognitive Services sind cloudbasierte Dienste mit REST-APIs und Clientbibliothek-SDKs, die Sie dabei unterstützen, Ihre Anwendungen mit kognitiver Intelligenz auszustatten. Für das Hinzufügen kognitiver Features zu Ihren Anwendungen sind keine KI- oder Data Science-Qualifikationen erforderlich. Azure Cognitive Services umfassen verschiedene KI-Dienste zur Erstellung kognitiver Lösungen, die sehen, hören, sprechen, verstehen und sogar Entscheidungen treffen können.

## <a name="categories-of-cognitive-services"></a>Kategorien von Cognitive Services

Der Cognitive Services-Katalog mit kognitivem Verständnis ist in fünf Hauptkategorien unterteilt:

* Bildanalyse
* Spracheingabe
* Sprache
* Entscheidung
* Suchen,

In den folgenden Abschnitten dieses Artikels werden die zugehörigen Dienste dieser fünf Kategorien aufgelistet.

## <a name="vision-apis"></a>Bildanalyse-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Maschinelles Sehen](./computer-vision/index.yml "Maschinelles Sehen")|Über den Dienst für maschinelles Sehen haben Sie Zugriff auf erweiterte kognitive Algorithmen für die Bildverarbeitung und die Rückgabe von Informationen. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Verwenden der Clientbibliothek für maschinelles Sehen](./computer-vision/quickstarts-sdk/client-library.md).|
|[Custom Vision Service](./custom-vision-service/index.yml "Custom Vision Service")|Der Custom Vision-Dienst ermöglicht Ihnen das Erstellen, Bereitstellen und Optimieren eigener Bildklassifizierungen. Eine Bildklassifizierung ist ein KI-Dienst, der basierend auf den visuellen Merkmalen in einem Bild Bezeichnungen auf Bilder anwendet. |
|[Gesichtserkennung](./face/index.yml "Gesicht")| Der Gesichtserkennungsdienst ermöglicht den Zugriff auf erweiterte Algorithmen zur Gesichtserkennung, wodurch die Ermittlung von Gesichtsmerkmalen sowie die Gesichtserkennung ermöglicht wird. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Verwenden der Clientbibliothek für die Gesichtserkennung](./face/quickstarts/client-libraries.md).|

## <a name="speech-apis"></a>Spracherkennungs-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Speech-Dienst](./speech-service/index.yml "Speech-Dienst")|Der Spracherkennungsdienst erweitert Anwendungen um sprachaktivierte Features. Der Speech-Dienst umfasst verschiedene Funktionen wie Spracherkennung, Sprachsynthese, Sprachübersetzung und vieles mehr.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Sprache-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Azure Cognitive Service für Sprache](./language-service/index.yml "Sprachdienst")| Azure Cognitive Service für Sprache bietet mehrere Features zur Verarbeitung natürlicher Sprache (Natural Language Processing, NLP), um Text zu verstehen und zu analysieren.|
|[Language Understanding (LUIS)](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) ist ein cloudbasierter Konversations-KI-Dienst, der benutzerdefinierte Machine Learning-Intelligenz auf natürliche Konversationssprachtexte eines Benutzers anwendet, um die allgemeine Bedeutung vorherzusagen sowie relevante und detaillierte Informationen abzurufen. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Erstellen einer neuen App im LUIS-Portal](./luis/luis-get-started-create-app.md).|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker ermöglicht es Ihnen, aus Ihren teilstrukturierten Inhalten einen Frage- und Antwortdienst zu erstellen. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Erstellen, Trainieren und Veröffentlichen der QnA Maker-Wissensdatenbank](./qnamaker/quickstarts/create-publish-knowledge-base.md).|
|[Translator](./translator/index.yml "Übersetzer")|Translator ermöglicht eine maschinenbasierte Textübersetzung in Quasi-Echtzeit.|

## <a name="decision-apis"></a>Entscheidungs-APIs

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Anomalieerkennung](./anomaly-detector/index.yml "Anomalieerkennung") |Die Anomalieerkennung bietet Ihnen die Möglichkeit, Abweichungen in Ihren Zeitreihendaten zu überwachen und zu erkennen. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Verwenden der Anomalieerkennungs-Clientbibliothek](./anomaly-detector/quickstarts/client-libraries.md).|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Der Content Moderator bietet die Überwachung auf möglicherweise anstößige, unerwünschte und risikobehaftete Inhalte. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Verwenden der Content Moderator-Clientbibliothek](./content-moderator/client-libraries.md).|
|[Personalisierung](./personalizer/index.yml "Personalisierung")|Mit der Personalisierung können Sie die beste Benutzeroberfläche für Ihre Benutzer auswählen und dabei in Echtzeit von deren Verhalten lernen. Informationen zu den ersten Schritten mit dem Dienst finden Sie unter [Schnellstart: Personalisierungsclientbibliothek](./personalizer/quickstart-personalizer-sdk.md).|

## <a name="search-apis"></a>Such-APIs

> [!NOTE]
> Suchen Sie [Azure Cognitive Search](../search/index.yml)? Cognitive Services wird zwar für einige Aufgaben verwendet, doch handelt es sich dabei um eine andere Suchtechnologie, die andere Szenarien unterstützt.

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Bing-News-Suche](/azure/cognitive-services/bing-news-search/ "Bing News-Suche")|Die Bing-News-Suche gibt eine Liste von Nachrichtenartikeln zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Bing-Videosuche](/azure/cognitive-services/Bing-Video-Search/ "Bing-Videosuche")|Die Bing-Videosuche gibt eine Liste von Videos zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Bing-Websuche](./bing-web-search/index.yml "Bing-Websuche")|Die Bing-Websuche gibt eine Liste von Suchergebnissen zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Bing-Vorschlagssuche](/azure/cognitive-services/Bing-Autosuggest "Bing-Vorschlagssuche")|Mit der Bing-Vorschlagssuche können Sie einen Teil eines Suchbegriffs an Bing senden und erhalten dann eine Liste mit vorgeschlagenen Abfragen.|
|[Benutzerdefinierte Bing-Suche](/azure/cognitive-services/bing-custom-search "Benutzerdefinierte Bing-Suche")|Die benutzerdefinierte Bing-Suche ermöglicht das Erstellen einer maßgeschneiderten Suchbenutzeroberfläche für Themen, die Sie interessieren.|
|[Bing-Entitätssuche](/azure/cognitive-services/bing-entities-search/ "Bing-Entitätssuche")|Die Bing-Entitätssuche gibt Informationen zu Entitäten zurück, die nach Einschätzung von Bing für eine Abfrage des Benutzers relevant sind.|
|[Bing-Bildersuche](/azure/cognitive-services/bing-image-search "Bing-Bildersuche")|Die Bing-Bildersuche gibt eine Liste von Bildern zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|
|[Visuelle Bing-Suche](/azure/cognitive-services/bing-visual-search "Visuelle Bing-Suche")|Die visuelle Bing-Suche gibt Informationen zu einem Bild zurück, z. B. visuell ähnliche Bilder, Einkaufsquellen für abgebildete Elemente und verwandte Suchvorgänge.|
|[Bing-Suche für ortsansässige Unternehmen](/azure/cognitive-services/bing-local-business-search/ "Bing-Suche für ortsansässige Unternehmen")| Mit der Bing-API für die Suche nach ortsansässigen Unternehmen können Ihre Anwendungen basierend auf Suchabfragen Kontakt- und Standortdaten von Unternehmen in der näheren Umgebung suchen.|
|[Bing-Rechtschreibprüfung](/azure/cognitive-services/bing-spell-check/ "Bing-Rechtschreibprüfung")|Die Bing-Rechtschreibprüfung ermöglicht es Ihnen, kontextbezogene Grammatik- und Rechtschreibprüfungen durchzuführen.|

## <a name="get-started-with-cognitive-services"></a>Erste Schritte mit Cognitive Services

Machen Sie sich anhand von praktischen Schnellstartanleitungen mit der Erstellung von Cognitive Services-Ressourcen vertraut. Folgende Methoden stehen zur Verfügung:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure-Portal")
* [Azure-Befehlszeilenschnittstelle](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-Clientbibliotheken](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure Resource Manager-Vorlagen](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager-Vorlagen")

## <a name="using-cognitive-services-in-different-development-environments"></a>Verwenden von Cognitive Services in verschiedenen Entwicklungsumgebungen

Mit Azure und Cognitive Services stehen Ihnen verschiedene Entwicklungsoptionen zur Verfügung. Hierzu zählen unter anderem folgende:

* Automatisierungs- und Integrationstools wie Logic Apps und Power Automate
* Bereitstellungsoptionen wie Azure Functions und App Service 
* Cognitive Services-Docker-Container für sicheren Zugriff
* Tools wie Apache Spark, Azure Databricks, Azure Synapse Analytics und Azure Kubernetes Service für Big Data-Szenarien 

Weitere Informationen finden Sie unter [Entwicklungsoptionen für Cognitive Services](./cognitive-services-development-options.md).

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Sicheres Verwenden von Cognitive Services

Azure Cognitive Services bietet ein mehrschichtiges Sicherheitsmodell einschließlich [Authentifizierung](authentication.md "authentication") über Azure Active Directory-Anmeldeinformationen, eines gültigen Ressourcenschlüssels und [Azure Virtual Networks](cognitive-services-virtual-networks.md "Virtuelle Azure-Netzwerke").

## <a name="containers-for-cognitive-services"></a>Container für Cognitive Services

 Azure Cognitive Services bietet mehrere Docker-Container, mit denen Sie lokal die gleichen APIs verwenden können, die in Azure verfügbar sind. Die Verwendung dieser Container gibt Ihnen die Flexibilität, Cognitive Services aus Compliance-, Sicherheits- oder anderen betrieblichen Gründen näher an Ihre Daten heranzubringen. Erfahren Sie mehr über [Containerunterstützung in Azure Cognitive Services](cognitive-services-container-support.md "Cognitive Services-Container").

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die APIs in Cognitive Services werden in einem wachsenden Netzwerk mit von Microsoft verwalteten Rechenzentren gehostet. Informationen zur regionalen Verfügbarkeit der einzelnen APIs finden Sie in der [Liste mit den Azure-Regionen](https://azure.microsoft.com/regions "Liste der Azure-Regionen").

Suchen Sie nach einer Region, die noch nicht unterstützt wird? Teilen Sie uns dies mit, indem Sie im [UserVoice-Forum](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858) eine Featureanfrage erstellen.

## <a name="supported-cultural-languages"></a>Unterstützte Kultursprachen

Cognitive Services unterstützt eine Vielzahl von Kultursprachen auf Dienstebene. Sie finden die Sprachenverfügbarkeit für jede API in der [Liste unterstützter Sprachen](language-support.md "Liste der unterstützten Sprachen").

## <a name="certifications-and-compliance"></a>Zertifizierungen und Compliance

Cognitive Services hat Zertifizierungen wie die CSA STAR-Zertifizierung, FedRAMP Moderate und HIPAA BAA erhalten. Sie können Zertifizierungen für eigene Überwachungen und Sicherheitsüberprüfungen [herunterladen](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "Download verfügbar ist").

Informationen zum Datenschutz und zur Datenverwaltung finden Sie im [Trust Center](https://servicetrust.microsoft.com/ "Trust Center").

## <a name="support"></a>Support

Für Cognitive Services stehen verschiedene Supportoptionen zur Verfügung, die Sie bei der Entwicklung intelligenter Anwendungen unterstützen. Bei spezifischen Fragen kann Ihnen außerdem die starke Entwicklercommunity von Cognitive Services behilflich sein. Eine vollständige Liste der verfügbaren Optionen finden Sie unter [Support- und Hilfeoptionen für Azure Cognitive Services](cognitive-services-support-options.md "Support- und Hilfeoptionen für Azure Cognitive Services").

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Cognitive Services-Kontos](cognitive-services-apis-create-account.md "Erstellen eines Cognitive Services-Kontos")
* [Planen und Verwalten von Kosten für Azure Cognitive Services](plan-manage-costs.md)
