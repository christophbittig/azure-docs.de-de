---
title: Anpassen eines Personenmodells in Azure Video Analyzer for Media (früher Video Indexer) – Azure
description: Dieser Artikel vermittelt einen Überblick darüber, was ein Personenmodell in Azure Video Analyzer for Media (früher Video Indexer) ist und wie es angepasst werden kann.
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 1d76c1124be542a22707b358ce1d050dfac380f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614862"
---
# <a name="customize-a-person-model-in-video-analyzer-for-media"></a>Anpassen eines Personenmodells in Azure Video Analyzer for Media

Azure Video Analyzer for Media (früher Video Indexer) unterstützt die Erkennung von Prominenten in Ihren Videos. Die Funktion zur Erkennung von Prominenten umfasst ungefähr eine Million Gesichter, die auf häufig angeforderten Datenquellen wie IMDB, Wikipedia und den wichtigsten LinkedIn-Influencern basieren. Gesichter, die vom Azure Video Analyzer for Media nicht erkannt werden, werden trotzdem erfasst, jedoch nicht benannt. Kunden können benutzerdefinierte Personenmodelle erstellen und Azure Video Analyzer for Media aktivieren, um Gesichter zu erkennen, die standardmäßig nicht erkannt werden. Kunden können diese Personenmodelle erstellen, indem sie den Namen einer Person mit Bilddateien des Gesichts der Person kombinieren.  

Wenn Ihr Konto unterschiedliche Anwendungsfälle abdeckt, können Sie davon profitieren, dass Sie mehrere Personenmodelle pro Konto erstellen können. Wenn der Inhalt in Ihrem Konto z. B. in verschiedene Kanäle sortiert werden soll, möchten Sie vielleicht für jeden Kanal ein eigenes Personenmodell erstellen. 

> [!NOTE]
> Jedes Personenmodell unterstützt bis zu 1 Million Personen und jedes Konto hat ein Limit von 50 Personenmodellen. 

Sobald ein Modell erstellt wurde, können Sie es verwenden, indem Sie die Modell-ID eines bestimmten Personenmodells beim Hochladen/Indizieren oder erneuten Indizieren eines Videos angeben. Beim Trainieren eines neuen Gesichts für ein Video wird das bestimmte benutzerdefinierte Modell, dem das Video zugeordnet war, aktualisiert. 

Wenn Sie keine Unterstützung für Mehrpersonenmodelle benötigen, weisen Sie Ihrem Video beim Hochladen/Indizieren oder erneuten Indizieren keine Personenmodell-ID zu. In diesem Fall verwendet Azure Video Analyzer for Media das Standardpersonenmodell in Ihrem Konto. 

Sie können die Azure Video Analyzer for Media-Website verwenden, um Gesichter zu bearbeiten, die in einem Video erkannt wurden, und um mehrere benutzerdefinierte Personenmodelle in Ihrem Konto zu verwalten, wie im Thema [Anpassen eines Personenmodells mithilfe einer Website](customize-person-model-with-website.md) beschrieben. Sie können auch die API verwenden. Dies wird unter  [Anpassen von Personenmodellen mithilfe von APIs](customize-person-model-with-api.md) beschrieben.
