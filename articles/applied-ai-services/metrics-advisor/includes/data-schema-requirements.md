---
title: Datenschemaanforderungen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: f00c25396405678312e4c18a345840d628c15848
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114340857"
---
Metrics Advisor ist ein Dienst für die Anomalieerkennung in Zeitreihen sowie für die Diagnose und Analyse von Zeitreihen. Da es sich hierbei um einen KI-gestützten Dienst handelt, wird das verwendete Modell mit Ihren Daten trainiert. Der Dienst akzeptiert Tabellen mit aggregierten Daten und folgenden Spalten:

* **Measure** (erforderlich): Ein Measure ist ein grundlegender bzw. einheitenspezifischer Ausdruck und ein quantifizierbarer Wert der Metrik. Er bezeichnet eine oder mehrere Spalte(n) mit numerischen Werten.
* **Zeitstempel** (optional): Maximal eine Spalte vom Typ `DateTime` oder `String`. Wird diese Spalte nicht festgelegt, wird der Zeitstempel als Startzeit des jeweiligen Erfassungszeitraums festgelegt. Formatieren Sie den Zeitstempel wie folgt: `yyyy-MM-ddTHH:mm:ssZ`. 
* **Dimension** (optional): Eine Dimension umfasst einen oder mehrere Kategoriewerte. Mit der Kombination dieser Werte wird eine bestimmte univariate Zeitreihe identifiziert, z. B. Land, Sprache, Mandant usw. Die Dimensionsspalten können einen beliebigen Datentyp aufweisen. Achten Sie bei Verwendung großer Mengen von Spalten und Werten darauf, dass nicht übermäßig viele Dimensionen verarbeitet werden.

Hier sehen Sie ein Beispiel für das erwartete Metrikschema: 

<!-- ![Screenshot of metrics schema example](../media/tutorial/metric-schema.png) -->

Aggregieren Sie Ihre Daten im Voraus entsprechend dem erwarteten Metrikschema, wenn Sie eine Datenquelle wie ADLS, Azure Blob... verwenden, die eine Datei als Metrikeingabe verwendet. Wenn Sie jedoch Datenquellen wie Azure SQL Server, Azure Data Explorer oder andere Quellen verwenden, die das Ausführen einer Abfrage zum Abrufen von Metrikdaten aus Quellen unterstützen, können Sie Aggregationsfunktionen verwenden, um Daten in einem erwarteten Schema zu aggregieren.
