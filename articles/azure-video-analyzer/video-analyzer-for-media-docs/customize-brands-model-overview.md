---
title: Anpassen eines Markenmodells in Azure Video Analyzer for Media (früher Video Indexer) – Azure
description: Dieser Artikel vermittelt einen Überblick darüber, was ein Markenmodell in Azure Video Analyzer for Media (früher Video Indexer) ist und wie es angepasst werden kann.
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: b698bce219468ee44a78aea7b8e449b603ecd975
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665661"
---
# <a name="customize-a-brands-model-in-video-analyzer-for-media"></a>Anpassen eines Markenmodells in Video Analyzer for Media

Azure Video Analyzer for Media (früher Video Indexer) unterstützt die Markenerkennung aus Sprache und visuellem Text während der Indizierung und Neuindizierung von Video- und Audioinhalten. Das Feature zur Erkennung von Marken identifiziert Erwähnungen von Produkten, Diensten und Unternehmen, die von der Markendatenbank von Bing vorgeschlagen werden. Wenn Microsoft beispielsweise in einem Video- oder Audioinhalt erwähnt wird oder im visuellen Text in einem Video angegeben wird, erkennt Video Analyzer for Media dies als Marke im Inhalt. Marken werden über den Kontext eindeutig von anderen Begriffen unterschieden.

Die Markenerkennung ist in einer Vielzahl von Geschäftsszenarien nützlich, z. B. bei der Archivierung und Erkennung von Inhalten, bei kontextabhängiger Werbung, Social Media-Analysen, Wettbewerbsanalyse im Einzelhandel und vielem mehr. Die Markenerkennung von Video Analyzer for Media ermöglicht es Ihnen, die Erwähnungen von Marken in Sprache und visuellem Text über die Markendatenbank von Bing und durch Anpassungen zu indizieren, indem Sie ein benutzerdefiniertes Markenmodell für jedes Video Analyzer for Media-Konto erstellen. Das Feature für benutzerdefinierte Markenmodelle ermöglicht das Auswählen, ob Video Analyzer for Media Marken aus der Bing-Markendatenbank erkennt, bestimmte Marken von der Erkennung ausschließt (im Grunde eine Liste nicht zulässiger Marken erstellt) und Marken einbezieht, die Teil Ihres Modells sein sollten, die möglicherweise nicht in der Bing-Markendatenbank enthalten sind (im Grunde eine Liste zulässiger Marken erstellt). Das von Ihnen erstellte benutzerdefinierte Markenmodell ist nur in dem Konto verfügbar, in dem Sie das Modell erstellt haben.

## <a name="out-of-the-box-detection-example"></a>Beispiel zur vorkonfigurierten Erkennung

In der Präsentation „Microsoft Build 2017 Day 2“ wird die Marke „Microsoft Windows“ mehrfach verwendet. Gelegentlich im Transkript, manchmal als sichtbarer Text, aber nie buchstäblich. Video Analyzer for Media erkennt mit hoher Genauigkeit auf der Grundlage des Kontexts, dass ein Begriff tatsächlich eine Marke ist, wobei über 90.000 Marken vorkonfiguriert sind und ständig aktualisiert werden. Video Analyzer for Media erkennt die Marke bei 02:25 in den Sprachinhalten und dann erneut bei 02:40 im sichtbaren Text, der Teil des Windows-Logos ist.

![Markenübersicht](./media/content-model-customization/brands-overview.png)

Beim Erwähnen von Fenstern (windows) im Kontext des Bauwesens wird das Wort „Windows“ nicht als Marke erkannt. Dasselbe gilt basierend auf fortschrittlichen Machine Learning-Algorithmen auch für „Box“, „Apple“, „Fox“ usw., da diese Algorithmen wissen, wie diese Wörter eindeutig anhand des Kontexts unterschieden werden. Die Markenerkennung funktioniert für alle unterstützten Sprachen.  

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie diese Themen, um zu erfahren, wie Sie bei eigenen Marken vorgehen:

[Anpassen des Markenmodells mit APIs](customize-brands-model-with-api.md)

[Anpassen des Markenmodells über die Website](customize-brands-model-with-website.md)
