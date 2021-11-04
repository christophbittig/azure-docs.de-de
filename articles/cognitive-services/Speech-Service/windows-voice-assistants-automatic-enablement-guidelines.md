---
title: Datenschutzrichtlinien für Sprachassistenten unter Windows
titleSuffix: Azure Cognitive Services
description: Die Anweisungen für die standardmäßige Sprachaktivierung eines Sprachassistenten.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 074335f9ccf9030a83354355a483ef5cbe0b58d3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500182"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Datenschutzrichtlinien für Sprachassistenten unter Windows

Es ist wichtig, dass Benutzern klare Informationen darüber gegeben werden, wie ihre Sprachdaten erfasst und verwendet werden, und es wichtig, den Benutzern die Kontrolle darüber zu geben, ob und wie diese Erfassung erfolgt. Diese Kernfacetten des Datenschutzes – *Offenlegung* und *Zustimmung* – sind insbesondere für in Windows integrierte Sprachassistenten wichtig, angesichts des bei ihrer Nutzung impliziten dauernden Zuhörens.

Entwickler, die Sprachassistenten unter Windows erstellen, müssen ihre Anwendungen mit klar verständlichen Benutzeroberflächenelementen ausstatten, die die Lauschfunktionen des Assistenten widerspiegeln.

> [!NOTE]
> Wird das Bereitstellen angemessener Offenlegungs- und Zustimmungsmöglichkeiten für eine Assistentenanwendung versäumt, auch nach Updates der Anwendung, steht der Assistent möglicherweise bis zur Behebung der Datenschutzprobleme nicht mehr zur Verfügung.

## <a name="minimum-requirements-for-feature-inclusion"></a>Mindestanforderungen für die Featureintegration

Windows-Benutzer können die Verfügbarkeit ihrer Assistentenanwendungen in **`Settings > Privacy > Voice activation`** anzeigen und steuern.

 > [!div class="mx-imgBorder"]
 > [![Screenshot: Optionen zum Steuern der Verfügbarkeit von Cortana](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Eintrag in den Datenschutzeinstellungen der Windows-Sprachaktivierung für eine Assistentenanwendung")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Um sich für die Aufnahme in diese Liste zu qualifizieren, wenden Sie sich an Microsoft unter winvoiceassistants@microsoft.com, um loszulegen. Standardmäßig müssen Benutzer die Sprachaktivierung für einen neuen Assistenten in **`Settings > Privacy > Voice Activation`** explizit aktivieren, womit eine Anwendung mithilfe von `ms-settings:privacy-voiceactivation` per Protokoll eine Verknüpfung erstellen kann. Eine zulässige Anwendung wird in der Liste angezeigt, sobald Sie ausgeführt wurde und die `Windows.ApplicationModel.ConversationalAgent`-APIs verwendet hat. Ihre Sprachaktivierungseinstellungen können geändert werden, sobald die Anwendung die Einwilligung des Benutzers für die Mikrofonnutzung erhalten hat.

Da die Windows-Datenschutzeinstellungen Informationen zur Funktionsweise der Sprachaktivierung umfassen und über eine Standardbenutzeroberfläche zum Kontrollieren der Berechtigung verfügt, sind Offenlegung und Einwilligung beide erfüllt. Der Assistent verbleibt in der zulässigen Liste, solange Folgendes nicht der Fall ist:

* Fehlleitung oder Fehlinformation des Benutzers über die Sprachaktivierung oder Behandlung von Sprachdaten durch den Assistenten
* Übermäßige Störung eines anderen Assistenten
* Verletzen jeglicher anderer relevanter Microsoft-Richtlinien

Wird einer der oben genannten Punkte erkannt, kann Microsoft einen Assistenten aus der zulässigen Liste entfernen, bis die Probleme behoben sind.

> [!NOTE]
> Unter allen Umständen erfordern Berechtigungen zur Sprachaktivierung die Berechtigung zur Mikrofonverwendung. Wenn eine Assistentenanwendung nicht über Mikrofonzugriff verfügt, ist sie nicht für Sprachaktivierung qualifiziert und wird in den Datenschutzeinstellungen der Sprachaktivierung in deaktiviertem Zustand angezeigt.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Zusätzliche Anforderungen für den Einschluss in die Einwilligung zum Mikrofongebrauch

Autoren von Assistenten können die Zustimmung zur Sprachaktivierung für ihre Benutzer einfacher und flüssiger gestalten, indem sie weitere Anforderungen erfüllen, um Offenlegung und Einwilligung adäquat zu erfüllen, ohne noch mal extra zur Seite mit den Einstellungen wechseln zu müssen. Nach der Genehmigung steht die Sprachaktivierung sofort zur Verfügung, sobald ein Benutzer der Assistentenanwendung Berechtigungen für die Mikrofonnutzung erteilt hat. Um sich hierfür zu qualifizieren, muss eine Assistentenanwendung Folgendes ausführen, **bevor** zur Einwilligung für die Mikrofonnutzung aufgefordert wird (z. B. mithilfe der `AppCapability.RequestAccessAsync`-API):

1. Zeigen Sie dem Benutzer an hervorgehobener Stelle deutlich an, dass die Anwendung in der Sprache eines Benutzers nach einem Schlüsselwort lauschen möchte, *auch bei nicht ausgeführter Anwendung*, und um Einwilligung des Benutzers bittet
1. Beziehen Sie relevante Informationen zu Datennutzungs- und Datenschutzrichtlinien ein, z. B. einen Link zu einer offiziellen Datenschutzerklärung.
1. Vermeiden Sie im Ablauf der Benutzeroberfläche, mit dem das Verhalten der Audioerfassung offengelegt wird, anweisende oder anleitende Formulierungen in jeder Form (beispielsweise „Klicken Sie bei der folgenden Anforderung auf ‚Ja‘“).

Wenn eine Anwendung alle oben genannten Punkte erreicht hat, ist Sie berechtigt, die Sprachaktivierungsfunktion in Verbindung mit der Einwilligung in die Mikrofonnutzung zu aktivieren. Wenden Sie sich an winvoiceassistants@microsoft.com, um weitere Informationen zu erhalten und sich eine Erfahrung der ersten Verwendung anzusehen.

> [!NOTE]
> Die Sprachaktivierung bei gesperrtem Gerät ist nicht für die automatische Aktivierung bei erteilten Mikrofonzugriff qualifiziert. Der Benutzer muss die Datenschutzseite der Sprachaktivierung aufsuchen, um den Zugang für einen Assistenten auch bei gesperrtem Gerät zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zu bewährten Methoden für Sprachassistenten unter Windows](windows-voice-assistants-best-practices.md)
