---
title: Aufrufen von Text Analytics for Health
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie medizinische Informationen mithilfe von Text Analytics for Health aus unstrukturiertem klinischem Text extrahiert und bezeichnet werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: d94e07b312bda98c6317e8a2b020510ffda800f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095531"
---
# <a name="how-to-use-text-analytics-for-health"></a>Verwenden von Text Analytics for Health

> [!IMPORTANT] 
> Text Analytics for Health ist eine Funktion, die „WIE BESEHEN“ und „MIT ALLEN MÄNGELN“ zur Verfügung gestellt wird. Text Analytics for Health ist nicht für die Verwendung als medizinisches Hilfsmittel, zur klinischen Unterstützung, als Diagnosetool oder als sonstige Technologie für den Einsatz in Diagnose, Therapie, Entschärfung, Behandlung oder Prävention von Krankheiten oder medizinischen Fällen vorgesehen oder verfügbar, und Microsoft erteilt keine Lizenzen oder Rechte zur Nutzung dieser Funktion für die beschriebenen Zwecke. Diese Funktion ist nicht als Ersatz für professionelle medizinische Beratung oder medizinische Gutachten, Diagnosen, Behandlungen oder das klinische Urteilsvermögen einer medizinischen Fachkraft konzipiert oder vorgesehen und sollte nicht als solcher eingesetzt werden. Jede Verwendung von Text Analytics for Health liegt in der alleinigen Verantwortung des Kunden. Der Kunde muss separat alle Quellvokabulare lizenzieren, die er gemäß den Bedingungen verwenden möchte, die für den [Anhang des UMLS-Metathesaurus-Lizenzvertrags](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html) oder einen zukünftigen entsprechenden Link festgelegt sind. Der Kunde ist für die Einhaltung dieser Lizenzbedingungen verantwortlich, einschließlich aller geografischen oder anderen anwendbaren Einschränkungen.


Text Analytics for Health kann zum Extrahieren und Bezeichnen relevanter medizinischer Informationen aus unstrukturierten Texten wie Arztbriefen, Entlassungsberichten, klinischen Dokumenten und elektronischen Gesundheitsakten verwendet werden.  Es gibt zwei Möglichkeiten zur Nutzung dieses Diensts: 

* Die webbasierte API und Clientbibliotheken (asynchron)
* Einen [Docker-Container](use-containers.md) (synchron)

## <a name="features"></a>Features

Text Analytics for Health führt Erkennung benannter Entitäten (Named Entity Recognition, NER), Beziehungsextrahierung, Entitätsnegation und Entitätsverknüpfung in englischsprachigem Text aus, um tiefere Einblicke in unstrukturierten klinischen und biomedizinischen Text zu erhalten. Die vollständige Liste der unterstützten Entitäten finden Sie in den von Text Analytics for Health zurückgegeben [Entitätskategorien](../concepts/health-entity-categories.md). Informationen zu Zuverlässigkeitsbewertungen finden Sie unter dem [Hinweis zur Transparenz](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

> [!TIP]
> Informationen zu den ersten Schritte zur Verwendung dieses Features finden Sie im [Schnellstartartikel](../quickstart.md). Sie können auch Beispielanforderungen in [Language Studio](../../language-studio.md) erstellen, ohne Code schreiben zu müssen.

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-text-analytics-for-health-model"></a>Angeben des Modells für Text Analytics for Health

Standardmäßig verwendet Text Analytics for Health das neueste verfügbare KI-Modell für Ihren Text. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das von Ihnen angegebene Modell wird zum Ausführen von Vorgängen verwendet, die von Text Analytics for Health bereitgestellt werden.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
| `2021-05-15` | `2021-05-15`   |

### <a name="text-analytics-for-health-container"></a>Textanalyse für Integritätscontainer

Der [Text Analytics for Health-Container](use-containers.md) verwaltet gegenüber der REST-API und den Clientbibliotheken die Modellversionen separat. Pro Containerimage ist nur jeweils eine Modellversion verfügbar.

| Endpunkt                        | Tag für Containerimage                     | Modellversion |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` (aktuellste)            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |

### <a name="input-languages"></a>Eingabesprachen

Aktuell [unterstützt](../language-support.md) Text Analytics for Health nur die englische Sprache. 

## <a name="submitting-data"></a>Übermitteln der Daten

Zum Senden einer API-Anforderung benötigen Sie Endpunkt und Schlüssel für Ihre Sprachressource.

> [!NOTE]
> Sie finden Schlüssel und Endpunkt für Ihre Sprachressource im Azure-Portal. Sie befinden sich auf der Seite mit dem **Schlüssel und Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Wenn Sie eine Anforderung über die REST-API oder Clientbibliothek senden, werden die Ergebnisse asynchron zurückgegeben. Wenn Sie den Docker-Container verwenden, werden sie synchron zurückgegeben.  


## <a name="getting-results-from-the-feature"></a>Abrufen von Ergebnissen aus dem Feature

Abhängig von Ihrer API-Anforderung und den Daten, die Sie an Text Analytics for Health übermitteln, erhalten Sie Folgendes:

[!INCLUDE [Text Analytics for health features](../includes/features.md)]


Wenn Sie dieses Feature asynchron verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung wie in der Antwort angegeben 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.

## <a name="data-limits"></a>Datengrenzwerte

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. Unterteilen Sie Text in die einzelnen Sätze, aus denen er besteht, um optimale Ergebnisse zu erzielen.
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Größe eines einzelnen Dokuments | 5\.120 Zeichen (gemessen von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)). |
| Maximale Größe der gesamte Anforderung | 1 MB |
| Max. Anzahl von Dokumenten pro Anforderung | Zehn für die webbasierte API, 1.000 für den Container |

Wenn ein Dokument den Grenzwert für die Zeichenanzahl überschreitet, verarbeitet die API kein Dokument, das die maximal zulässige Größe überschreitet und gibt einen Fehler wegen eines ungültigen Dokuments zurück. Wenn eine API-Anforderung mehrere Dokumente umfasst, fährt die API mit deren Verarbeitung fort, sofern sie sich innerhalb des Zeichenlimits befinden.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing). Diese Begrenzungen sind bei beiden Versionen der API identisch. Diese Ratenbegrenzungen gelten nicht für die Textanalyse für Integritätscontainer, für die keine Ratenbegrenzung festgelegt ist.

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| F0         | 100                 | 300                 |

## <a name="see-also"></a>Siehe auch

* [Übersicht über die Textanalyse für Gesundheitsdaten](../overview.md)
* [Entitätskategorien für Text Analytics for Health](../concepts/health-entity-categories.md)
