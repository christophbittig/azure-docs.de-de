---
title: 'Schnellstart: Clientbibliothek oder REST-API für die Formularerkennung'
titleSuffix: Azure Applied AI Services
description: Verwenden Sie die Clientbibliothek oder die REST-API für die Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: Formularverarbeitung, automatisierte Datenverarbeitung
ms.openlocfilehash: a1b355bdd67d9bf84a2c9ae24fb557a9e50fa1d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652526"
---
# <a name="quickstart-get-started-with-the-client-library-sdks-or-rest-api"></a>Schnellstart: Erste Schritte mit den Clientbibliothek-SDKs oder der REST-API

Hier finden Sie Informationen zu den ersten Schritten mit der Azure-Formularerkennung mithilfe der Programmiersprache Ihrer Wahl. Die Azure-Formularerkennung ist eine [Azure Applied AI Services](../../../applied-ai-services/index.yml)-Instanz und erlaubt Ihnen, unter Verwendung der Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung zu entwickeln. Identifizieren und extrahieren Sie Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellendaten und Ähnliches aus Formulardokumenten. Der Dienst gibt strukturierte Daten aus, die auch die Beziehungen in der ursprünglichen Datei umfassen. Die Formularerkennung kann per REST-API oder per SDK verwendet werden. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Belegen](#analyze-receipts)
* [Analysieren von Visitenkarten](#analyze-business-cards)
* [Analysieren von Rechnungen](#analyze-invoices)
* [Analysieren von Ausweisdokumenten](#analyze-identity-documents)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten benutzerdefinierter Modelle](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end