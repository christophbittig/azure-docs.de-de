---
title: 'Referenz: Formularerkennung – Clientbibliothek 3.0.0 und REST-API v2.0'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Clientbibliothek v3.0.0 oder die REST-API v2.0 für die Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 05/25/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp
keywords: Formularverarbeitung, automatisierte Datenverarbeitung
ms.openlocfilehash: 32dcfb1ba1e413e666e6a3d27ef9f03dbce81978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641752"
---
# <a name="reference-azure-form-recognizer-client-library-v300-and-rest-api-v20"></a>Referenz: Azure-Formularerkennung – Clientbibliothek v3.0.0 und REST-API v2.0

>[!IMPORTANT]
>
> * Dieser Leitfaden basiert auf dem SDK v3.0.0 und der REST-API v2.0 für die Azure-Formularerkennung. Dokumentation und Codebeispiele für die neueste Version finden Sie in unserem Artikel zum **[Schnellstart](../quickstarts/client-library.md)** .
>
>* Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services/cognitive-services-security.md).

Hier finden Sie Informationen zu den ersten Schritten mit der Azure-Formularerkennung mithilfe der Programmiersprache Ihrer Wahl. Die Azure-Formularerkennung ist eine [Azure Applied AI Service](../../../applied-ai-services/index.yml)-Instanz und erlaubt Ihnen, unter Verwendung der Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung zu entwickeln. Identifizieren und extrahieren Sie Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellendaten und Ähnliches aus Formulardokumenten. Der Dienst gibt strukturierte Daten aus, die auch die Beziehungen in der ursprünglichen Datei umfassen. Die Formularerkennung kann per REST-API oder per SDK verwendet werden. Führen Sie die nachfolgenden Schritte zum Installieren des SDK-Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK v3.0.0](includes/csharp-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK v3.0.0](includes/java-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK v3.0.0](includes/javascript-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK v3.0.0](includes/python-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API v2.0](includes/rest-api-v2-0.md)]

::: zone-end