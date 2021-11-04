---
title: 'Übersicht über die Sprechererkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Die Sprechererkennung bietet Algorithmen, mit denen Sprecher anhand ihrer individuellen Stimmmerkmale mithilfe von Sprachbiometrie überprüft und identifiziert werden. Die Sprechererkennung wird verwendet, um herauszufinden, wer spricht. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Sprechererkennungsdiensts.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: eur
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
keywords: Sprechererkennung, Sprachbiometrie
ms.openlocfilehash: 4096e04678c3ce0b10ac54a190f3074525469b54
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509460"
---
# <a name="what-is-speaker-recognition"></a>Was ist Sprechererkennung?

Die Sprechererkennung dient dazu, festzustellen, wer in einem Audioclip spricht. Der Dienst kann Sprecher anhand ihrer einzigartigen Stimmmerkmale mit Hilfe der Stimmbiometrie verifizieren und identifizieren. Wenn Sie Audiotrainingsdaten für einen einzelnen Sprechers bereitstellen, wird basierend auf den eindeutigen Stimmmerkmalen des Sprechers ein Registrierungsprofil erstellt. Sie können dann Audiostimmproben mit diesem Profil abgleichen, um zu überprüfen, ob es sich bei dem Sprecher um dieselbe Person handelt (Sprecherüberprüfung), oder Sie können Audiostimmproben mit einer *Gruppe* von registrierten Sprecherprofilen abgleichen, um zu sehen, ob sie mit einem Profil in der Gruppe übereinstimmen (Sprecheridentifizierung).

> [!IMPORTANT]
> Microsoft schränkt den Zugang zur Sprechererkennung ein. Beantragen Sie die Nutzung über die [Azure Cognitive Services Sprechererkennung Limited Access Review](https://aka.ms/azure-speaker-recognition). Nach der Genehmigung können Sie auf die APIs für die Sprechererkennung zugreifen. Weitere Informationen finden Sie unter [Beschränkter Zugang für Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition).

## <a name="speaker-verification"></a>Sprecherüberprüfung

Die Sprecherüberprüfung optimiert den Prozess für die Überprüfung einer registrierten Sprecheridentität mithilfe von Passphrasen oder einer Spracheingabe in Freiform. Sie kann verwendet werden, um Kunden sicher und reibungslos für eine Vielzahl von Lösungen zu verifizieren – angefangen bei der Identitätsüberprüfung von Kunden in Callcentern bis hin zu kontaktlosem Zugang zu Gebäuden.

### <a name="how-does-speaker-verification-work"></a>Wie funktioniert die Sprecherüberprüfung?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Sprecherüberprüfungs-Flussdiagramm.":::

Die Sprecherüberprüfung kann entweder textabhängig oder textunabhängig sein. Bei der **textabhängigen** Überprüfung müssen die Sprecher sowohl bei der Registrierung als auch bei der Überprüfung dieselbe Passphrase verwenden. Bei der **textunabhängigen** Überprüfung können Sprecher sowohl bei der Registrierung als auch bei der Überprüfung etwas beliebiges sagen.

Bei der **textabhängigen** Überprüfung wird die Stimme des Sprechers registriert, wenn er eine Passphrase aus vorgegebenen Phrasen spricht. Einerseits werden Stimmmerkmale aus der Audioaufzeichnung extrahiert, um eine eindeutige Stimmsignatur zu bilden, und andererseits wird die Passphrase erkannt. Die Stimmsignatur und die Passphrase werden verwendet, um den Sprecher zu überprüfen. 

**Bei der textunabhängigen** Verifizierung gibt es keine Einschränkungen für das, was der Sprecher während der Anmeldung sagt, abgesehen von der anfänglichen Aktivierungsphrase zur Aktivierung der Anmeldung. Es gibt keine Beschränkungen für die zu überprüfenden Audiomuster, da es nur Stimmmerkmale extrahiert, um die Ähnlichkeit zu bewerten. 

Die APIs sind nicht dafür vorgesehen zu bestimmen, ob das Audio von einer echten Person oder einer Imitation oder Aufzeichnung eines registrierten Sprechers stammt. 

## <a name="speaker-identification"></a>Sprecheridentifikation

Die Sprecheridentifikation wird verwendet, um die Identität eines unbekannten Sprechers innerhalb einer Gruppe registrierter Sprecher zu ermitteln. Sie ermöglicht es Ihnen, Spracheingaben einzelnen Sprechern zuzuordnen und einen Mehrwert aus Szenarios mit mehreren Sprechern zu ziehen:

* Supportlösungen für die Produktivität bei Remotemeetings 
* Erstellen von Gerätepersonalisierung mit mehreren Benutzern

### <a name="how-does-speaker-identification-work"></a>Wie funktioniert die Sprecheridentifikation?

Das Enrollment für die Sprecheridentifikation ist **textunabhängig**, d.h. es gibt keine Einschränkungen für das, was der Sprecher im Audio sagt, außer der anfänglichen Aktivierungsphrase zur Aktivierung des Enrollments. Ähnlich wie bei der Sprecherüberprüfung wird die Stimme des Sprechers in der Registrierungsphase aufgezeichnet, und die Stimmmerkmale werden extrahiert, um eine eindeutige Stimmsignatur zu erstellen. In der Identifikationsphase wird das Spracheingabebeispiel mit einer bestimmten Liste registrierter Stimmen verglichen (bis zu 50 in jeder Anforderung).

## <a name="data-security-and-privacy"></a>Datensicherheit und -schutz

Sprecheranmeldungsdaten werden in einem gesicherten System gespeichert, einschließlich der Sprachaudioinformationen für die Registrierung und der Sprachsignaturmerkmale. Die Sprachaudiodaten für die Registrierung wird nur beim Upgrade des Algorithmus verwendet, und die Merkmale müssen noch einmal extrahiert werden. Der Dienst speichert weder die Sprachaufzeichnung noch die extrahierten Stimmmerkmale, die während der Erkennungsphase an den Dienst gesendet werden. 

Sie steuern, wie lange Daten aufbewahrt werden sollen. Sie können über API-Aufrufe Registrierungsdaten für einzelne Sprecher erstellen, aktualisieren und entfernen. Beim Löschen des Abonnements werden auch alle mit dem Abonnement verbundenen Sprecherregistrierungsdaten gelöscht. 

Wie bei allen Cognitive Services-Ressourcen müssen Entwickler, die den Sprechererkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Sie müssen sicherstellen, dass Sie von den Benutzern die entsprechende Erlaubnis für die Sprechererkennung erhalten haben. Weitere Einzelheiten finden Sie in [Daten und Datenschutz für die Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition). Weitere Informationen finden Sie im Microsoft Trust Center auf der  [Seite zu Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) . 

## <a name="common-questions-and-solutions"></a>Häufige Fragen und Lösungen

| Frage | Lösung |
|---------|----------|
| Für welche Szenarien kann die Sprechererkennung verwendet werden? | Kundenüberprüfung in Callcentern, sprachbasierter Check-In von Patienten, Besprechungstranskription, Personalisierung von Geräten mit mehreren Benutzern|
| Was ist der Unterschied zwischen Identifizierung und Überprüfung? | Identifikation ist der Prozess der Erkennung, welches Mitglied aus einer Gruppe von Sprechern spricht. Mit der Überprüfung wird bestätigt, dass ein Sprecher mit einer bekannten oder **registrierten** Stimme übereinstimmt.|
| Was ist der Unterschied zwischen textabhängiger und textunabhängiger Überprüfung? | Bei der textabhängigen Überprüfung ist eine bestimmte Passphrase für Registrierung und Erkennung erforderlich. Die textunabhängige Verifizierung erfordert eine längere Stimmprobe, die mit einer bestimmten Aktivierungsphrase für die Registrierung beginnen muss, aber es kann alles gesprochen werden, auch während der Erkennung.|
| Welche Sprachen werden unterstützt? | Englisch, Französisch, Spanisch, Chinesisch, Deutsch, Italienisch, Japanisch und Portugiesisch |
| Welche Azure-Regionen werden unterstützt? | Sprechererkennung ist ein Vorschaudienst und derzeit nur in der Region „USA, Westen“ verfügbar.|
| Welche Audioformate werden unterstützt? | Mono 16 Bit, 16 kHz PCM-codiertes WAV |
| Die Antworten für **Akzeptieren** und **Ablehnen** sind nicht genau, wie optimieren Sie den Schwellenwert? | Da der optimale Schwellenwert je nach Szenario stark schwankt, entscheidet die API einfach auf Grundlage eines Standardschwellenwerts von 0,5, ob „Akzeptiert“ oder „Abgelehnt“ wird. Erfahreneren Benutzern wird empfohlen, die Standardentscheidung außer Kraft zu setzen und das Ergebnis auf Grundlage ihres eigenen Szenarios zu optimieren. |
| Können Sie einen Sprecher mehrmals registrieren? | Ja, bei der textabhängigen Überprüfung können Sie einen Sprecher bis zu 50-mal registrieren. Bei der textunabhängigen Überprüfung oder der Sprecheridentifikation können Sie die Registrierung mit bis zu 300 Sekunden Audioeingabe durchführen. |
| Welche Daten werden in Azure gespeichert? | Registrierungsaudiodaten werden im Dienst gespeichert, bis das Stimmprofil [gelöscht](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments) wird. Erkennungsaudiobeispiele werden nicht aufbewahrt oder gespeichert. |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> * Lesen Sie den [Artikel zu den Grundlagen](./get-started-speaker-recognition.md) der Sprechererkennung, um einen Überblick über gängige Entwurfsmuster zu erhalten, die Sie in Ihren Anwendungen verwenden können.
> * Weitere Informationen finden Sie im [Videotutorial](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) zur textunabhängigen Sprecherüberprüfung.

## <a name="responsible-use-of-ai"></a>Verantwortungsvolle Verwendung von KI
> [!div class="ResponsibleUseOfAI"]
> * [Transparenzhinweis zur Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/transparency-note-speaker-recognition)
> * [Merkmale und Grenzen der Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/characteristics-and-limitations-speaker-recognition)
> * [Beschränkter Zugang zur Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition)
> * [Leitfaden für die Integration und den verantwortungsvollen Umgang mit Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/guidance-integration-responsible-use-speaker-recognition)
> * [Daten und Datenschutz für die Sprechererkennung](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition)
