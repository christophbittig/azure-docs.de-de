---
title: Verwenden der Clientbibliothek-SDKs oder der REST-API für die Formularerkennung
titleSuffix: Azure Applied AI Services
description: Verwenden Sie ein Clientbibliothek-SDK oder die REST-API für die Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0402041121e34902d9801307dad8a759f8fe9516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094926"
---
# <a name="use-form-recognizer-sdks-or-rest-api"></a>Verwenden von SDKs oder der REST-API für die Formularerkennung

 In dieser Anleitung erfahren Sie, wie Sie die Azure-Formularerkennung zu Ihren Anwendungen und Workflows hinzufügen, indem Sie ein SDK in einer Programmiersprache Ihrer Wahl oder die REST-API verwenden. Die Azure-Formularerkennung ist ein cloudbasierter Dienst von Azure Applied AI Services, bei dem maschinelles Lernen genutzt wird, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren des Layouts](#analyze-layout)
* [Analysieren von Belegen](#analyze-receipts)
* [Analysieren von Visitenkarten](#analyze-business-cards)
* [Analysieren von Rechnungen](#analyze-invoices)
* [Analysieren von Ausweisdokumenten](#analyze-id-documents)
* [Trainieren eines benutzerdefinierten Modells](#train-a-custom-model)
* [Analysieren von Formularen mit einem benutzerdefinierten Modell](#analyze-forms-with-a-custom-model)
* [Verwalten benutzerdefinierter Modelle](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/how-to-guides/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/how-to-guides/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/how-to-guides/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/how-to-guides/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/how-to-guides/rest-api.md)]

::: zone-end
