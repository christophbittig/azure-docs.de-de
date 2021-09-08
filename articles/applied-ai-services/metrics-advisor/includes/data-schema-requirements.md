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
ms.openlocfilehash: 32fc672bf41c35881baf082b5694d1800084889d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745955"
---
Azure Metrics Advisor ist ein Dienst für die Anomalieerkennung in Zeitreihen sowie für die Diagnose und Analyse von Zeitreihen. Da es sich hierbei um einen KI-gestützten Dienst handelt, wird das verwendete Modell mit Ihren Daten trainiert. Der Dienst akzeptiert Tabellen mit aggregierten Daten und folgenden Spalten:

* **Measure** (erforderlich): Ein Measure ist ein grundlegender bzw. einheitenspezifischer Ausdruck und ein quantifizierbarer Wert der Metrik. Er bezeichnet eine oder mehrere Spalte(n) mit numerischen Werten.
* **Zeitstempel** (optional): Maximal eine Spalte vom Typ `DateTime` oder `String`. Wird diese Spalte nicht festgelegt, wird der Zeitstempel als Startzeit des jeweiligen Erfassungszeitraums festgelegt. Formatieren Sie den Zeitstempel wie folgt: `yyyy-MM-ddTHH:mm:ssZ`. 
* **Dimension** (optional): Eine Dimension umfasst einen oder mehrere Kategoriewerte. Mit der Kombination dieser Werte wird eine bestimmte univariate Zeitreihe identifiziert (z. B. Land, Sprache, Mandant usw.). Die Dimensionsspalten können einen beliebigen Datentyp aufweisen. Achten Sie bei Verwendung großer Mengen von Spalten und Werten darauf, dass nicht übermäßig viele Dimensionen verarbeitet werden.

Wenn Sie Datenquellen wie Azure Data Lake Storage oder Azure Blob Storage verwenden, können Sie Ihre Daten entsprechend dem erwarteten Metrikschema aggregieren. Der Grund dafür ist, dass diese Datenquellen eine Datei als Metrikeingabe verwenden.

Wenn Sie Datenquellen wie Azure SQL oder Azure Data Explorer verwenden, können Sie Aggregationsfunktionen verwenden, um Daten in Ihrem erwarteten Schema zu aggregieren. Der Grund dafür ist, dass diese Datenquellen das Ausführen einer Abfrage zum Abrufen von Metrikdaten aus Quellen unterstützen.

