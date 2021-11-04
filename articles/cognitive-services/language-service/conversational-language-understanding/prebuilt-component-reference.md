---
title: Unterstützte vordefinierte Entitätskomponenten
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, welche Entitäten in Conversational Language Understanding automatisch erkannt werden können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: fc7188b647e6e299390179c68bcf9c9742a018f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095551"
---
# <a name="supported-prebuilt-entity-components"></a>Unterstützte vordefinierte Entitätskomponenten

Mit Conversational Language Understanding können Sie vordefinierte Komponenten zu Entitäten hinzufügen. Vordefinierte Komponenten prognostizieren automatisch häufig auftretende Typen aus Äußerungen. Weitere Informationen zu Komponenten finden Sie im Artikel [Entitätskomponenten](concepts/entity-components.md).

## <a name="reference"></a>Verweis

Die folgenden vordefinierten Komponenten sind in Conversational Language Understanding verfügbar.

| type | BESCHREIBUNG | Unterstützte Sprachen |
| --- | --- | --- |
| Quantity.Age | Alter einer Person oder Sache Beispiele: „30 Jahre alt“, „9 Monate alt“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Quantity.Number | Eine Kardinalzahl im numerischen Format oder Textformat Beispiele: „dreißig“, „23“, „14,5“, „zweieinhalb“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Quantity.Percentage | Ein Prozentwert mit Prozentsymbol oder dem Wort „Prozent“ Beispiele: „10 %“, „5,6 Prozent“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Quantity.Ordinal | Eine Ordinalzahl im numerischen Format oder Textformat Beispiele: „erstes“, „zweites“, „letztes“, „10.“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Quantity.Dimension | Dimensionen wie Länge, Entfernung, Volumen, Fläche und Geschwindigkeit Beispiele: „zwei Meilen“, „650 Quadratkilometer“, „35 km/h“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Quantity.Temperature | Temperatur in Celsius oder Fahrenheit Beispiele: „32 F“, „34 Grad Celsius“, „2 °C“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Quantity.Currency | Währungsbeträge einschließlich Währung Beispiele: „1000,00 US-Dollar“, „20,00 £“, „67,5 Mrd. USD“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Datetime | Datums- und Zeitangaben Beispiele: „23. Juni 1976“, „7 Uhr“, „18:49 Uhr“, „morgen um 19 Uhr“, „nächste Woche“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Email | E-Mail-Adressen Beispiele: „user@contoso.com“, „user_name@contoso.com“, „user.name@contoso.com“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| Rufnummer | US-Telefonnummern Beispiele: „123-456-7890“, „+1 123 456 7890“, „(123)456-7890“ | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |
| URL | Website-URLs und Links | Englisch, Chinesisch, Französisch, Italienisch, Deutsch, Portugiesisch (Brasilien), Spanisch |

## <a name="prebuilt-components-in-multilingual-projects"></a>Vordefinierte Komponenten in mehrsprachigen Projekten

In mehrsprachigen Konversationsprojekten können Sie jede der vordefinierten Komponenten aktivieren. Die Komponente wird nur prognostiziert, wenn die Sprache der Abfrage von der vordefinierten Komponente unterstützt wird. Die Sprache kann in der Anforderung angegeben werden. Sie ist standardmäßig auf die primäre Sprache der Anwendung festgelegt, wenn sie nicht angegeben wird.

## <a name="next-steps"></a>Nächste Schritte

[Entitätskomponenten](concepts/entity-components.md) 
