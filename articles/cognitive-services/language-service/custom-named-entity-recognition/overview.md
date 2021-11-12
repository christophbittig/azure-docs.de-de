---
title: Was ist die benutzerdefinierte Erkennung benannter Entitäten (Named Entity Recognition, NER) in Azure Cognitive Service for Language (Vorschau)?
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die benutzerdefinierte Erkennung benannter Entitäten (NER) verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: bac6068c02ea4f253176a65061d11604104c2bd5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439042"
---
# <a name="what-is-custom-named-entity-recognition-ner-preview"></a>Was ist benutzerdefinierte Erkennung benannter Entitäten (Vorschau)?

Die benutzerdefinierte Erkennung benannter Entitäten ist ein Feature von [Azure Cognitive Service für Language](../overview.md). Es handelt sich um einen cloudbasierten API-Dienst, der Machine Learning-Intelligenz anwendet, um Ihnen die Erstellung benutzerdefinierter Modelle für textbezogene benutzerdefinierte NER-Aufgaben zu ermöglichen.

Die benutzerdefinierte Erkennung benannter Entitäten wird als Teil der benutzerdefinierten Features in [Azure Cognitive für Language](../overview.md) angeboten. Mit diesem Feature können Benutzer benutzerdefinierte KI-Modelle erstellen, um domänenspezifische Entitäten aus unstrukturiertem Text wie Verträgen oder Finanzdokumenten zu extrahieren. Durch das Erstellen eines Projekts für die benutzerdefinierte NER können Entwickler Daten iterativ markieren, trainieren und auswerten sowie die Modellleistung verbessern, bevor sie für die Nutzung verfügbar gemacht werden. Die Qualität der markierten Daten wirkt sich erheblich auf die Modellleistung aus. Um das Erstellen und Anpassen Ihres Modells zu vereinfachen, steht im Dienst ein benutzerdefiniertes Webportal zur Verfügung, auf das über [Language Studio](https://aka.ms/languageStudio) zugegriffen werden kann. Sie können problemlos erste Schritte mit dem Dienst ausführen, indem Sie [diese Schnellstartanleitung](quickstart.md) befolgen. 
 
Diese Dokumentation enthält die folgenden Arten von Artikeln:

* [Schnellstarts](quickstart.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* Die Artikel zu [Konzepten](concepts/evaluation-metrics.md) enthalten Erläuterungen der Dienstfunktionen und -features.
* [Anleitungen](how-to/tag-data.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.

## <a name="example-usage-scenarios"></a>Beispiele für Nutzungsszenarien

### <a name="information-extraction"></a>Informationsextraktion

Zahlreiche Finanz- und Rechtsorganisationen extrahieren und normalisieren täglich Daten aus Tausenden von komplexen unstrukturierten Texten, z. B. aus Kontoauszügen, Verträgen oder Bankformularen. Anstatt diese Formulare manuell zu verarbeiten, kann die benutzerdefinierte Erkennung benannter Entitäten diesen Prozess automatisieren und so den Kosten- und Zeitaufwand verringern.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>Knowledge Mining zum Verbessern/Anreichern der semantischen Suche

Search dient als wichtige Grundlage für jede App, mit der Textinhalte für Benutzer angezeigt werden. Häufige Szenarien sind die Katalog- oder Dokumentsuche, die Suche nach Einzelhandelsprodukten oder das Knowledge Mining bei Data Science.Viele Unternehmen aus verschiedenen Branchen möchten eine umfangreiche Suchumgebung für private, heterogene Inhalte erstellen, die sowohl strukturierte als auch unstrukturierte Dokumente umfassen. Als Teil ihrer Pipeline können Entwickler benutzerdefinierte NER verwenden, um Entitäten aus den Texten zu extrahieren, die für ihre Branche relevant sind. Mithilfe dieser Entitäten kann die Indizierung der Datei für eine stärker angepasste Sucherfahrung angereichert werden. 

### <a name="audit-and-compliance"></a>Überwachung und Compliance

Anstatt umfangreiche Textdateien manuell zu überprüfen, um Richtlinien zu überwachen und anzuwenden, können IT-Abteilungen in Finanz- oder Rechtsunternehmen die benutzerdefinierte Erkennung benannter Entitäten verwenden, um automatisierte Lösungen zu erstellen. Diese Lösungen helfen bei der Durchsetzung von Compliancerichtlinien und bei der Aufstellung der erforderlichen Geschäftsregeln auf der Grundlage von Knowledge Mining-Pipelines, die strukturierte und unstrukturierte Inhalte verarbeiten.

## <a name="application-development-lifecycle"></a>Lebenszyklus der Anwendungsentwicklung

Die Verwendung von benutzerdefinierter NER umfasst in der Regel mehrere verschiedene Schritte. 

:::image type="content" source="../custom-classification/media/development-lifecycle.png" alt-text="Lebenszyklus der Entwicklung" lightbox="../custom-classification/media/development-lifecycle.png":::

1. **Definieren des Schemas:** Sie müssen Ihre Daten kennen und die Entitäten identifizieren, die Sie extrahieren möchten. Vermeiden Sie Mehrdeutigkeit.

2. **Markieren von Daten:** Die Datenmarkierung ist ein wichtiger Faktor beim Ermitteln der Modellleistung. Achten Sie auf eine präzise, konsistente und vollständige Markierung.
    1. **Präzise Markierung:** Markieren Sie jede Entität immer mit ihrem richtigen Typ. Nehmen Sie nur das auf, was Sie extrahieren möchten, und vermeiden Sie unnötige Daten im Tag.
    2. **Konsistente Markierung:** Eine Entität muss in allen Dateien über dasselbe Tag verfügen.
    3. **Vollständige Markierung:** Markieren Sie alle Instanzen der Entität in allen Dateien.

3. **Trainieren des Modells:** Ihr Modell beginnt mit dem Lernen auf der Grundlage der markierten Daten.

4. **Anzeigen der Details zur Modellauswertung:** Zeigen Sie nach Abschluss des Trainings die Auswertungsdetails und die Leistung des Modells an.

5. **Verbessern des Modells:** Nachdem Sie die Details der Modellauswertung überprüft haben, können Sie sich darüber informieren, wie Sie das Modell verbessern können.

6. **Bereitstellen des Modells**: Die Bereitstellung eines Modells bedeutet, dass es zur Verwendung verfügbar gemacht wird.

7. **Extrahieren von Entitäten:** Verwenden Sie Ihre benutzerdefinierten Modelle für Entitätsextraktionsaufgaben.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [diesen Schnellstartartikel](quickstart.md), um mit der Verwendung der benutzerdefinierten Textklassifizierung zu beginnen.  

* Lesen Sie beim Durchlaufen des Lebenszyklus der Anwendungsentwicklung das [Glossar](glossary.md), um mehr über die Begriffe zu erfahren, die in der Dokumentation für dieses Feature verwendet werden. 

* Informationen zu Aspekten wie [regionale Verfügbarkeit](service-limits.md#regional-availability) finden Sie in den [Diensteinschränkungen](service-limits.md).
