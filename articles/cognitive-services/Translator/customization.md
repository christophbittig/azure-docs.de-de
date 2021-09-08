---
title: Anpassen der Übersetzung – Translator
titleSuffix: Azure Cognitive Services
description: Verwenden Sie den Microsoft Translator-Hub zum Erstellen Ihres eigenen Systems für die maschinelle Übersetzung mit der Terminologie und dem Stil, den Sie bevorzugen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 68ae3735fc1f58ebbf8d7afd6a098e7520880058
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228754"
---
# <a name="customize-your-text-translations"></a>Anpassen von Textübersetzungen

Benutzerdefinierter Translator ist ein Feature des Translator-Diensts, mit dem Sie die erweiterte neuronale maschinelle Übersetzung von Microsoft Translator anpassen können, wenn Sie Translator (nur Version 3) zum Übersetzen verwenden.

Das Feature kann mit [Cognitive Services Speech](../speech-service/index.yml) auch zum Anpassen der Sprachübersetzung verwendet werden.

## <a name="custom-translator"></a>Benutzerdefinierter Translator

Mit dem benutzerdefinierten Translator können Sie neuronale Übersetzungssysteme erstellen, die die Terminologie verstehen, die in Ihrem Unternehmen und der Branche verwendet wird. Das angepasste Übersetzungssystem wird dann in vorhandene Anwendungen, Workflows und Websites integriert.

### <a name="how-does-it-work"></a>Wie funktioniert dies?

Verwenden Sie zuvor übersetzte Dokumente (Broschüren, Webseiten, Dokumentationen usw.), um ein Übersetzungssystem zu erstellen, das die domänenspezifische Terminologie und den Stil besser als ein Standardübersetzungssystem widerspiegelt. Benutzer können Dokumente in den folgenden Dateiformaten hochladen: TMX, XLIFF, TXT, DOCX und XLSX.  

Das System akzeptiert auch Daten, die parallel auf Dokumentebene parallel sind, jedoch noch nicht auf Satzebene ausgerichtet wurden. Wenn Sie über Zugriff auf Versionen mit dem gleichen Inhalt in mehreren Sprachen verfügen, die sich jedoch in separaten Dokumenten befinden, kann der benutzerdefinierte Translator Sätze über mehrere Dokumente hinweg automatisch abgleichen.  Das System kann auch einsprachige Daten in einer oder beiden Sprachen verwenden, um die parallelen Trainingsdaten zu ergänzen, um die Übersetzungen zu verbessern.

Das benutzerdefinierte System ist dann über einen regulären Aufruf von Translator mit dem Category-Parameter verfügbar.

Mit dem entsprechenden Typ und der Menge von Trainingsdaten ist es nicht ungewöhnlich, unter Verwendung des benutzerdefinierten Translators Gewinne von fünf bis zehn oder sogar mehr BLEU-Punkten für die Übersetzungsqualität zu erwarten.

Weitere Informationen über die verschiedenen Anpassungsstufen basierend auf verfügbaren Daten finden Sie im [Benutzerleitfaden für den benutzerdefinierten Translator](./custom-translator/overview.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Set up a customized language system using Custom Translator (Einrichten eines angepassten Sprachsystems mit dem benutzerdefinierten Translator)](./custom-translator/overview.md)
