---
title: Audioverarbeitung – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Eine Übersicht über die Audioverarbeitung und die Funktionen von Microsoft Audio Stack.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: f0bc80a9c248b9171a89bead1971cb7d5f4ce0fe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095345"
---
# <a name="audio-processing"></a>Audioverarbeitung

Audioverarbeitung bezieht sich auf Verbesserungen, die auf einen Audiodatenstrom angewendet werden, um die Audioqualität zu verbessern. Eine Reihe von Verbesserungen wird häufig als Stapel für die Audioverarbeitung bezeichnet. Das Ziel der Verbesserung der Audioqualität kann in verschiedene Szenarien wie Sprachverarbeitung und Telekommunikation weiter segmentiert werden. Beispiele für häufige Verbesserungen sind die automatische Verstärkungssteuerung (Automatic Gain Control, AGC), die Rauschunterdrückung und der akustische Echoabbruch (Acoustic Echo Cancellation, AEC).

Verschiedene Szenarien/Anwendungsfälle erfordern unterschiedliche Optimierungen, die das Verhalten des Audioverarbeitungsstapels beeinflussen. In Telekommunikationsszenarien wie Telefonanrufen ist es beispielsweise akzeptabel, dass das Audiosignal geringfügig verzerrt wird, nachdem die Verarbeitung angewendet wurde. Dies liegt daran, dass Menschen die Sprache weiterhin mit hoher Genauigkeit verstehen können. Es ist jedoch nicht akzeptabel und störend, wenn eine Person ihre eigene Stimme in einem Echo hören kann. Dies steht im Gegensatz zu Sprachverarbeitungsszenarien, bei denen verzerrte Audiodaten sich negativ auf die Genauigkeit eines durch maschinelles Lernen gelernten Spracherkennungsmodells auswirken können, es jedoch akzeptabel ist, kleinere Echo-Restwerte zu haben.

## <a name="microsoft-audio-stack"></a>Microsoft Audio Stack

Microsoft Audio Stack ist eine Reihe von Verbesserungen, die für Sprachverarbeitungsszenarien optimiert sind. Dies schließt Beispiele wie Schlüsselworterkennung und Spracherkennung ein. Sie besteht aus verschiedenen Erweiterungen/Komponenten, die auf dem Audioeingabesignal arbeiten:

* **Rauschunterdrückung** - Verringern Sie die Hintergrundgeräusche.
* **Balkenforming** - Lokalisieren Sie den Ursprung des Sounds, und optimieren Sie das Audiosignal mithilfe mehrerer Mikrofone.
* **Dereverberation** - Reduzieren Sie die Reflexionen von Sound von Oberflächen in der Umgebung.
* **Akustik-Echoabbruch:** - Unterdrücken Sie die Audiowiedergabe des Geräts, während die Mikrofoneingabe aktiv ist.
* **Automatische Verstärkungssteuerung** - Passen Sie die Stimmebene der Person dynamisch an, um weiche Lautsprecher, lange Entfernungen oder nicht kalibrierte Mikrofone zu berücksichtigen.

[ ![Blockdiagramm der Verbesserungen von Microsoft Audio Stack. ](media/audio-processing/mas-block-diagram.png) ](media/audio-processing/mas-block-diagram.png#lightbox)

Microsoft Audio Stack unterstützt eine Vielzahl von Microsoft-Produkten:
* **Windows** - Microsoft Audio Stack ist die Standardpipeline für die Sprachverarbeitung bei Verwendung der Kategorie Sprachaudio. 
* **Microsoft Teams Displays and Microsoft Teams Room-Geräte** - Microsoft Teams Displays- und Teams Room-Geräte verwenden den Microsoft Audio Stack, um hochwertige freihnde, sprachbasierte Erfahrungen mit Cortana.

### <a name="pricing"></a>Preise

Die Verwendung des Microsoft-Audiostapels mit dem Speech SDK ist ohne Kosten.

### <a name="minimum-requirements-to-use-microsoft-audio-stack"></a>Mindestanforderungen für die Verwendung von Microsoft Audio Stack

Microsoft Audio Stack kann von jedem Produkt oder jeder Anwendung verwendet werden, die die folgenden Anforderungen erfüllen kann:
* **Unformatierte Audiodaten** - Microsoft Audio Stack benötigt unformatierte (d.h. nicht verarbeitete) Audiodaten als Eingabe, um die besten Ergebnisse zu erzielen. Die Bereitstellung von Audiodaten, die bereits verarbeitet werden, schränkt die Fähigkeit des Audiostapels ein, Verbesserungen in hoher Qualität durchzuführen.
* **Mikrofongeometrien** - Geometrieinformationen zu jedem Mikrofon auf dem Gerät sind erforderlich, um alle Verbesserungen des Microsoft-Audiostapels ordnungsgemäß durchzuführen. Zu den Informationen gehören die Anzahl der Mikrofone, ihre physische Anordnung und die Koordinaten. Es werden bis zu 16 Mikrofoneingabekanäle unterstützt. 
* **Loopback oder Referenzaudio** - Ein Audiokanal, der die vom Gerät abgespielte Audiodatei darstellt, ist erforderlich, um einen akustischen Echoabbruch durchzuführen. 
* Der Microsoft Audio Stack im Speech Devices SDK unterstützt das Komprimieren von Daten nur für Abtastraten, die ein ganzzahliges Vielfaches von 16 KHz sind. Eine Minimale Samplingrate von 16 kHz ist erforderlich. 32-Bit IEEE Little-Endian vom Typ „float“, 32-Bit Little-Endian vom Typ „signed int“, 24-Bit Little-Endian vom Typ „signed int“, 16-Bit Little-Endian vom Typ „signed int“ und 8-Bit vom Typ „signed int“.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über die Speech SDK-Integration von Microsoft Audio Stack.](audio-processing-speech-sdk.md)
