---
title: 'Empfehlungen und Richtlinien für die Schlüsselworterkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Eine Übersicht über Empfehlungen und Richtlinien bei der Verwendung der Schlüsselworterkennung.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: a6b7a9c072951a7010403c9e54dff844eb910d95
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490660"
---
# <a name="recommendations-and-guidelines-for-keyword-recognition"></a>Empfehlungen und Richtlinien für die Schlüsselworterkennung

In diesem Artikel wird beschrieben, wie Sie Ihr Schlüsselwort auswählen, dessen Genauigkeitsmerkmale optimieren und Ihre Benutzerumgebungen mit der Schlüsselwortüberprüfung entwerfen. 

## <a name="choosing-an-effective-keyword"></a>Auswählen eines effektiven Schlüsselworts

Das Erstellen eines effektiven Schlüsselworts ist wichtig, um sicherzustellen, dass Ihr Produkt konsistent und genau antwortet. Berücksichtigen Sie bei der Auswahl eines Schlüsselworts die folgenden Richtlinien.

> [!NOTE]
> Die folgenden Beispiele sind in englischer Sprache, aber die Richtlinien gelten für alle Sprachen, die von „Benutzerdefiniertes Schlüsselwort“ unterstützt werden. Eine Liste aller unterstützten Sprachen finden Sie unter [Sprachunterstützung](language-support.md#custom-keyword-and-keyword-verification).

- Es sollte nicht länger als zwei Sekunden dauern, es auszusprechen.
- Wörter mit 4 bis 7 Silben eignen sich am besten. So ist „Hey, Computer“ beispielsweise ein gutes Schlüsselwort. Nur „Hey“ ist ein schlechtes Aktivierungswort.
- Schlüsselwörter sollten allgemeinen Ausspracheregeln entsprechen, die für die Muttersprache Ihrer Endbenutzer spezifisch sind.
- Durch ein einzigartiges Wort oder ein Kunstwort, das die allgemeinen Ausspracheregeln befolgt, könnten False Positive-Ergebnisse verringert werden. „Computerama“ würde sich beispielsweise gut als Schlüsselwort eignen.
- Wählen Sie kein gängiges Wort aus. Die Wörter „eat“ und „go“ werden beispielsweise häufig in gewöhnlichen Konversationen gesagt. Sie können zu höheren als den gewünschten Akzeptanzraten für falsche Begriffe für Ihr Produkt führen.
- Vermeiden Sie die Verwendung eines Schlüsselworts, das verschiedene Aussprachevarianten haben könnte. Benutzern müsste die „richtige“ Aussprache bekannt sein, um ihr Produkt per Sprache aktivieren zu können. „509“ kann beispielsweise als „five zero nine“, „five oh nine“ oder als „five hundred and nine“ ausgesprochen werden. „R.E.I.“ kann als „r-e-i“ oder als „ray“ ausgesprochen werden. „Live“ kann „/līv/“ oder „/liv/“ ausgesprochen werden.
- Verwenden Sie keine Sonderzeichen, Symbole oder Ziffern. „Go#“ und „20 + cats“ können problematische Schlüsselwörter sein. „Go sharp“ oder „twenty plus cats“ kann jedoch funktionieren. Sie können weiterhin die Symbole in Ihrem Branding sowie Marketing und Dokumentation zur Untermauerung der richtigen Aussprache verwenden.


## <a name="user-experience-recommendations-with-keyword-verification"></a>Empfehlungen für die Benutzererfahrung mit der Schlüsselwortüberprüfung

Bei einem Szenario mit mehrstufiger Schlüsselworterkennung, in dem die [Schlüsselwortüberprüfung](keyword-recognition-overview.md#keyword-verification) verwendet wird, können Anwendungen auswählen, wann der Endbenutzer über eine Schlüsselworterkennung benachrichtigt wird. Die Empfehlung zum Rendern eines visuellen oder akustischen Indikators besteht darin, sich auf Antworten des Diensts für die Schlüsselwortüberprüfung zu verlassen:

![Richtlinie für die Benutzererfahrung bei der Optimierung der Genauigkeit.](media/custom-keyword/kw-verification-ux-accuracy.png)

Dies gewährleistet ein optimales Erlebnis in Bezug auf die Genauigkeit, um die vom Benutzer wahrgenommenen Auswirkungen der Akzeptanz falscher Begriffe zu minimieren, verursacht jedoch zusätzliche Wartezeiten.

Für Anwendungen, die eine Optimierung der Wartezeit erfordern, können Anwendungen dem Benutzer leichte und unauffällige Indikatoren auf der Grundlage der gerätebasierten Schlüsselworterkennung zur Verfügung stellen. Beispielsweise das Aufleuchten eines LED-Musters oder das Pulsieren eines Symbols. Die Indikatoren können weiter bestehen, wenn die Schlüsselwortüberprüfung mit einer Schlüsselwortannahme antwortet, oder sie können verworfen werden, wenn die Antwort eine Schlüsselwortablehnung ist:

![Richtlinie für die Benutzererfahrung bei der Optimierung der Wartezeit.](media/custom-keyword/kw-verification-ux-latency.png)

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen des Speech SDK](speech-sdk.md)
* [Weitere Informationen zu Sprach-Assistenten](voice-assistants.md)
