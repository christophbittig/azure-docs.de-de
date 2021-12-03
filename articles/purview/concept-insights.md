---
title: Grundlegendes zu Erkenntnisse-Berichten in Azure Purview
description: In diesem Artikel werden die Erkenntnisse in Azure Purview erläutert.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 405be2ec031e95a6c59128b5f23310a4c0a711ac
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157940"
---
# <a name="understand-insights-in-azure-purview"></a>Grundlegendes zu Erkenntnissen in Azure Purview

Dieser Artikel bietet eine Übersicht über das Erkenntnisse-Feature in Azure Purview.

Erkenntnisse zählen zu den tragenden Säulen von Purview. Diese Funktion bietet Kunden eine einzelne Ansicht in ihrem Katalog und soll darüber hinaus Datenquellenadministratoren, Geschäftsbenutzern, Data Stewards, Data Officers und Sicherheitsadministratoren spezifische Erkenntnisse vermitteln. Derzeit verfügt Purview über die folgenden Insights-Berichte, die den Kunden während der öffentlichen Vorschau von Insight zur Verfügung stehen werden.

> [!IMPORTANT]
> Azure Purview-Erkenntnisse befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="asset-insights"></a>Ressourcen-Erkenntnisse

Dieser Bericht zeigt Ihre Datenumgebung aus der Vogelperspektive und unterteilt sie beispielsweise nach Dimensionen wie Quelltyp, Klassifizierung und Dateigröße. Dieser Bericht richtet sich an verschiedene Arten von Stakeholdern in den Bereichen Data Governance und Katalogisierung, die den Status ihrer DataMap nach Klassifizierung und Dateierweiterungen kennen möchten.

Der Bericht bietet umfassende Erkenntnisse mittels Diagrammen und KPIs und geht später ausführlich auf bestimmte Anomalien wie z. B. falsch platzierte Dateien ein. Außerdem unterstützt der Bericht auch eine komfortable End-to-End-Benutzeroberfläche, in der Kunden die Anzahl von Ressourcen mit einer bestimmten Klassifizierung anzeigen, Informationen nach Quelltypen und obersten Ordnern aufschlüsseln und auch die Liste der Ressourcen zur weiteren Untersuchung anzeigen können.

> [!NOTE]
> File Extension Insights wurde mit Asset Insights zusammengelegt und bietet nun einen umfassenderen Trendbericht, der das Wachstum der Datengröße nach Dateierweiterung zeigt. Weitere Informationen finden Sie unter [Asset Insights](asset-insights.md) 
>
>

## <a name="scan-insights"></a>Scan-Erkenntnisse

Anhand des Berichts können die Datenquellenadministratoren den allgemeinen Zustand der Scans nachvollziehen - wie viele erfolgreich waren, wie viele fehlgeschlagen sind und wie viele abgebrochen wurden. Dieser Bericht liefert ein Statusupdate für Scans, die innerhalb der letzten sieben Tage oder letzten 30 Tage im Purview-Konto ausgeführt wurden.

Außerdem können Administratoren anhand des Berichts eingehend untersuchen, bei welchen Scans bei welchen spezifischen Quelltypen Fehler aufgetreten sind. Um den Benutzern weitere Untersuchungen zu ermöglichen, unterstützt der Bericht sie bei der Navigation in der Scanverlaufseite in der Benutzeroberfläche „Quellen“.

## <a name="glossary-insights"></a>Glossarerkenntnisse

Dieser Bericht gibt den Data Stewards einen Statusbericht über das Glossar. Datenverantwortliche können diesen Bericht einsehen, um die Verteilung der Glossarbegriffe nach Status zu verstehen und zu erfahren, wie viele Glossarbegriffe mit Assets verknüpft sind und wie viele noch nicht mit einem Asset verknüpft sind. Geschäftsbenutzer können sich auch über die Vollständigkeit ihrer Glossarbegriffe informieren. 

Dieser Bericht fasst die wichtigsten Punkte zusammen, auf die sich ein Data Steward konzentrieren muss, um ein vollständiges und brauchbares Glossar für seine Organisation zu erstellen. Stewards können auch von "Glossary Insights" zum "Glossar" navigieren, um Änderungen an einem bestimmten Glossarbegriff vorzunehmen.

## <a name="classification-insights"></a>Klassifizierungs-Erkenntnisse

Dieser Bericht gibt Auskunft darüber, wo sich die Verschlusssachen befinden, welche Klassifizierungen bei einer Überprüfung gefunden wurden und wie die Verschlusssachen selbst aussehen. Es ermöglicht Stewards, Kuratoren und Sicherheitsadministratoren, die Arten von Informationen zu verstehen, die sich im Datenbestand ihrer Organisation befinden. 

In Azure Purview ähneln Klassifizierungen Betrefftags und dienen zum Kennzeichnen und Identifizieren von Inhalten eines bestimmten Typs in Ihrer Datenumgebung.

Identifizieren Sie anhand des Berichts „Klassifizierungs-Erkenntnisse“ Inhalte mit bestimmten Klassifizierungen, und verstehen Sie erforderliche Aktionen wie z. B. das Erhöhen der Sicherheit der Repositorys oder das Verschieben von Inhalten an einen sichereren Ort.

Weitere Informationen finden Sie unter [Klassifizierungs-Erkenntnisse zu Ihren Daten von Azure Purview](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Vertraulichkeitsbezeichnungs-Erkenntnisse

Dieser Bericht enthält Details zu den bei einer Überprüfung gefundenen Sensitivitätskennzeichnungen sowie eine Aufschlüsselung der gekennzeichneten Dateien selbst. Er ermöglicht Sicherheitsadministratoren, die Sicherheit der in der Datenumgebung Ihrer Organisation gefundenen Informationen sicherzustellen. 

In Azure Purview werden Vertraulichkeitsbezeichnungen verwendet, um die Klassifizierungstypkategorien sowie die Gruppensicherheitsrichtlinien zu ermitteln, die Sie auf die einzelnen Kategorien anwenden möchten.

Verwenden Sie den Bericht „Vertraulichkeitsbezeichnungs-Erkenntnisse“, um die in ihren Inhalten gefundenen Vertraulichkeitsbezeichnungen zu identifizieren und erforderliche Aktionen wie das Verwalten des Zugriffs auf bestimmte Repositorys oder Dateien zu verstehen.

Weitere Informationen finden Sie unter [Vertraulichkeitsbezeichnungs-Erkenntnisse zu Ihren Daten in Azure Purview](sensitivity-insights.md).

## <a name="next-steps"></a>Nächste Schritte

* [Glossarerkenntnisse](glossary-insights.md)
* [Überprüfungserkenntnisse](scan-insights.md)
* [Klassifizierungs-Erkenntnisse](./classification-insights.md)
