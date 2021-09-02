---
title: 'Schnellstart: Erstellen und Verwenden einer Lernschleife mit dem SDK – Personalisierung'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Ihre Wissensdatenbank mithilfe der Personalisierungsclientbibliothek erstellen und verwalten.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: Personalisierung, Azure Personalisierung, Machine Learning
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: f35b20c47c12130423badcac3373be27c01e13c0
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829859"
---
# <a name="quickstart-personalizer-client-library"></a>Schnellstart: Personalisierungsclientbibliothek

Zeigen Sie personalisierten Inhalt in dieser Schnellstartanleitung mit dem Personalisierungsdienst an.

Führen Sie erste Schritte mit der Personalisierungsclientbibliothek aus. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben.

 * Rangfolge-API: Basierend auf den Echtzeitinformationen, die Sie zum Inhalt und zum Kontext angeben, wird das beste Element aus den Aktionen ausgewählt.
 * Relevanz-API: Sie bestimmen die Relevanzbewertung basierend auf Ihren Geschäftsanforderungen und senden sie mit dieser API an die Personalisierung. Diese Bewertung kann ein einzelner Wert (z. B. „1“ für „Gut“ und „0“ für „Schlecht“) oder ein Algorithmus sein, den Sie anhand Ihrer Geschäftsanforderungen erstellt haben.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Funktionsweise der Personalisierung](how-personalizer-works.md)
> [Verwendungsbereich für die Personalisierung](where-can-you-use-personalizer.md)
