---
title: Was ist die benutzerdefinierte Klassifizierung (Vorschau) in Azure Cognitive Services for Language?
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die benutzerdefinierte Textklassifizierung verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: ec68964d1d0e43bf15e66750677326eca305784f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053063"
---
# <a name="what-is-custom-text-classification-preview"></a>Worum handelt es sich bei der benutzerdefinierten Textklassifizierung (Vorschau)?

Die benutzerdefinierte Textklassifizierung ist eines der Features von [Azure Cognitive Service for Language](../overview.md). Es handelt sich um einen cloudbasierten API-Dienst, der Machine Learning-Intelligenz anwendet, um Ihnen die Erstellung benutzerdefinierter Modelle für Textklassifizierungsaufgaben zu ermöglichen. 

Die benutzerdefinierte Textklassifizierung wird als Teil der benutzerdefinierten Features in Azure Cognitive for Language angeboten. Mit diesem Feature können Benutzer benutzerdefinierte KI-Modelle erstellen, um Text in benutzerdefinierte Kategorien einzuteilen, die vom Benutzer vorab definiert wurden. Durch das Erstellen eines Projekts für die benutzerdefinierte Klassifizierung können Entwickler Daten iterativ markieren, trainieren und auswerten sowie die Modellleistung verbessern, bevor sie für die Nutzung verfügbar gemacht werden. Die Qualität der markierten Daten wirkt sich erheblich auf die Modellleistung aus. Um das Erstellen und Anpassen Ihres Modells zu vereinfachen, steht im Dienst ein benutzerdefiniertes Webportal zur Verfügung, auf das über [Language Studio](https://aka.ms/languageStudio) zugegriffen werden kann. Sie können problemlos erste Schritte mit dem Dienst ausführen, indem Sie [diese Schnellstartanleitung](quickstart.md) befolgen. 

Benutzerdefinierte Textklassifizierung unterstützt zwei Arten von Projekten: 

* **Klassifizierung mit einzelner Bezeichnung:** Sie können für jede Datei Ihres Datasets eine einzelne Klasse zuweisen. Beispielsweise kann ein Drehbuch nur als „Action“ oder als „Thriller“ klassifiziert werden. 
* **Klassifizierung mit mehreren Bezeichnungen:** Sie können für jede Datei Ihres Datasets mehrere Klassen zuweisen. Beispielsweise kann ein Drehbuch als „Action“ oder als „Action und Thriller“ klassifiziert werden.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* [Schnellstarts](quickstart.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* Die Artikel zu [Konzepten](concepts/evaluation.md) enthalten Erläuterungen der Dienstfunktionen und -features.
* [Anleitungen](how-to/tag-data.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.

## <a name="example-usage-scenarios"></a>Beispiele für Nutzungsszenarien

### <a name="automatic-emailsticket-triage"></a>Automatische E-Mails/Ticketselektierung

Supportcenter aller Art erhalten Tausende bis Hunderttausende von E-Mails/Tickets mit unstrukturiertem Text, Freiformtext und Anlagen. Die rechtzeitige Überprüfung, Bestätigung und Weiterleitung an fachliche Ansprechpartner in internen Teams ist von entscheidender Bedeutung. Die E-Mail-Selektierung in diesem Umfang, an der Personen zur Überprüfung und Weiterleitung an die richtigen Abteilungen beteiligt sind, nimmt jedoch Zeit und wertvolle Ressourcen in Anspruch. Mithilfe der benutzerdefinierten Klassifizierung können eingehende Texte analysiert und ihre Inhalte kategorisiert werden, damit sie automatisch an die zuständige Abteilung weitergeleitet werden, um die erforderlichen Maßnahmen zu ergreifen.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>Knowledge Mining zum Verbessern/Anreichern der semantischen Suche

Search dient als wichtige Grundlage für Apps, mit der Textinhalte für Benutzer angezeigt werden. Häufige Szenarien sind die Katalog- oder Dokumentsuche, die Suche nach Einzelhandelsprodukten oder das Knowledge Mining bei Data Science.Viele Unternehmen aus verschiedenen Branchen möchten eine umfangreiche Suchumgebung für private, heterogene Inhalte erstellen, die sowohl strukturierte als auch unstrukturierte Dokumente umfassen. Als Teil ihrer Pipeline können Entwickler die benutzerdefinierte Klassifizierung verwenden, um Text in Klassen einzuteilen, die für ihre Branche relevant sind. Mithilfe der vorhergesagten Klassen kann die Indizierung der Datei für eine stärker angepasste Sucherfahrung angereichert werden. 

## <a name="project-development-lifecycle"></a>Lebenszyklus der Projektentwicklung

Das Erstellen eines benutzerdefinierten Klassifizierungsprojekts umfasst in der Regel mehrere verschiedene Schritte. 

:::image type="content" source="media/development-lifecycle.png" alt-text="Lebenszyklus der Entwicklung" lightbox="media/development-lifecycle.png":::

Führen Sie die folgenden Schritte aus, um Ihr Modell optimal zu nutzen:

1. **Definieren des Schemas:** Sie müssen Ihre Daten kennen und die Klassen identifizieren, zwischen denen Sie unterscheiden möchten, um Mehrdeutigkeit zu vermeiden.

2. **Markieren von Daten:** Die Qualität der Datenmarkierung ist ein wichtiger Faktor beim Ermitteln der Modellleistung. Markieren Sie alle Dateien, die Sie in das Training einschließen möchten. Dateien, die zur gleichen Klasse gehören, sollten immer die gleiche Klasse aufweisen. Wenn Sie über eine Datei verfügen, die in zwei Klassen unterteilt werden kann, verwenden Sie Projekte für die **Klassifizierung mehrerer Klassen**. Vermeiden Sie mehrdeutige Klassen, und stellen Sie sicher, dass Ihre Klassen eindeutig voneinander getrennt werden können, insbesondere bei Projekten zur Klassifizierung mit einer einzelnen Klasse.

3. **Trainieren des Modells:** Ihr Modell beginnt mit dem Lernen auf der Grundlage der markierten Daten.

4. **Anzeigen von Details zur Modellauswertung:** Zeigen Sie die Auswertungsdetails für Ihr Modell an, um zu ermitteln, wie gut es bei der Einführung für neue Daten funktioniert.

5. **Verbessern des Modells:** Arbeiten Sie an der Verbesserung der Modellleistung, indem Sie die falschen Modellvorhersagen und die Datenverteilung untersuchen.

6. **Bereitstellen des Modells:** Durch die Bereitstellung eines Modells wird dieses zur Verwendung über die [Analyse-API](https://aka.ms/ct-runtime-swagger) zur Verfügung gestellt.

7. **Klassifizieren von Text:** Verwenden Sie Ihr benutzerdefiniertes Modell für Textklassifizierungsaufgaben.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [diesen Schnellstartartikel](quickstart.md), um mit der Verwendung der benutzerdefinierten Textklassifizierung zu beginnen.  

* Lesen Sie beim Durchlaufen des Lebenszyklus der Projektentwicklung das [Glossar](glossary.md), um mehr über die Begriffe zu erfahren, die in der Dokumentation für dieses Feature verwendet werden. 

* Informationen zu Aspekten wie [regionale Verfügbarkeit](service-limits.md#regional-availability) finden Sie in den [Diensteinschränkungen](service-limits.md).
