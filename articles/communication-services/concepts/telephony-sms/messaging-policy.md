---
title: Nachrichtenrichtlinie
titleSuffix: An Azure Communication Services concept document
description: Enthält Informationen zu Richtlinien für SMS-Nachrichten.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2281dac86281d01ec6f033062b87236a780d3bc7
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259746"
---
# <a name="azure-communication-services-messaging-policy"></a>Azure Communication Services: Nachrichtenrichtlinie

Azure Communication Services bedeutet eine Transformation der Art und Weise, wie unsere Kunden sich mit ihren eigenen Kunden vernetzen. Diese Dienste ermöglichen die Erstellung von umfassenden benutzerdefinierten Kommunikationsumgebungen, bei denen dieselben Unternehmensdienste genutzt werden, die auch Microsoft Teams und Skype zugrunde liegen. Integrieren Sie Funktionen für SMS-Nachrichten in Ihre Kommunikationslösungen, damit Sie Ihre Kunden jederzeit und an jedem Ort erreichen und den benötigten Support leisten können. Für den Anfang müssen Sie nur einige Anforderungen an das Messaging und ein paar Branchenstandards kennen.

Uns ist bewusst, dass das Erlernen der für Nachrichten geltenden Anforderungen ggf. nicht einfach ist, aber Sie können sich als Hilfestellung einfach die Abkürzung „COMS“ merken:

- C: Consent (Einwilligung)
- O: Opt-Out (Beendigung der Teilnahme)
- M: Message Content (Nachrichteninhalt)
- S: Spoofing

Wir haben diese Nachrichtenrichtlinie als Hilfe für Sie entwickelt, damit Sie die rechtlichen Anforderungen erfüllen können und über die empfohlenen bewährten Methoden informiert sind. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Einwilligung 

### <a name="what-is-consent"></a>Was bedeutet Einwilligung?

Die Einwilligung ist eine Vereinbarung zwischen Ihnen und dem Empfänger der Nachricht, die Ihnen das Senden von automatisierten Nachrichten an diese Person ermöglicht. Vor dem Senden der ersten Nachricht müssen Sie eine Einwilligung einholen. Sie sollten dem Empfänger auf eindeutige Weise mitteilen, dass dieser Ihnen bei diesem Vorgang seine Einwilligung für den Empfang Ihrer Nachrichten erteilt. Dieser Vorgang wird als Einholung einer „vorherigen ausdrücklichen Einwilligung“ von der Person bezeichnet, an die Sie Nachrichten senden möchten.

Die von Ihnen gesendeten Nachrichten müssen den Typ aufweisen, für deren Empfang der Empfänger seine Einwilligung erteilt hat, und sie dürfen nur an die Nummer gesendet werden, die Sie vom Empfänger erhalten haben. Falls Sie Nachrichten vom Typ „Information“ senden möchten, z. B. Terminerinnerungen oder Benachrichtigungen, kann die Einwilligung auch schriftlich oder mündlich erteilt werden. In schriftlicher Form muss die Einwilligung erfolgen, falls Sie Werbenachrichten senden möchten, z. B. Verkaufs- oder Marketingnachrichten zu einem Produkt oder einer Dienstleistung.

### <a name="how-do-you-obtain-consent"></a>Wie erhalten Sie die Einwilligung?

Sie können die Einwilligung auf verschiedene Arten erhalten, z. B.:

- Ein Benutzer gibt seine Telefonnummer auf einer Website ein 
- Ein Benutzer initiiert einen SMS-Austausch
- Ein Benutzer sendet ein Registrierungsschlüsselwort an Ihre Telefonnummer 
 
Unabhängig davon, wie die Einwilligung erteilt wird, müssen Sie und Ihre Kunden sicherstellen, dass die Einwilligung eindeutig ist. Dem Empfänger sollte der Umfang der Einwilligung klar sein.


### <a name="consent-requirements"></a>Anforderungen für die Einwilligung:

- Übermitteln Sie vor dem Einholen der Einwilligung eine entsprechende Handlungsaufforderung. Sie und Ihre Kunden sollten an potenzielle Nachrichtenempfänger eine Handlungsaufforderung übermitteln, in der diese eingeladen werden, an Ihrem Messagingprogramm teilzunehmen. Die Handlungsaufforderung sollte mindestens Folgendes enthalten: (1) Identität des Absenders, (2) eindeutige Anleitung für die Teilnahme, (3) Anleitung für die Beendigung der Teilnahme und (4) Informationen zu etwaigen Gebühren für Nachrichten.
- Die Einwilligung ist nicht übertragbar oder abtretbar. Eine Einwilligung, die Ihnen von einer Person erteilt wird, darf nicht auf unbeteiligte Dritte übertragen oder an diese verkauft werden. Falls Sie die Einwilligung einer Person für einen Drittanbieter einholen, müssen Sie die Person auf eindeutige Weise über diesen Drittanbieter informieren. Darüber hinaus müssen Sie auch angeben, dass die von Ihnen eingeholte Einwilligung nur für Nachrichten von diesem Drittanbieter gilt.
- Die Einwilligung ist zweckgebunden. Eine Person, die ihre Nummer für einen bestimmten Zweck angibt, erteilt hiermit ihre Einwilligung nur für den Empfang von Nachrichten zu diesem bestimmten Zweck und nur vom jeweiligen Absender. Vor dem Einholen der Einwilligung sollten Sie den jeweiligen Nachrichtenempfänger auf eindeutige Weise darüber informieren, ob dieser regelmäßig Nachrichten von Ihnen bzw. einem Partner erhält.

### <a name="consent-best-practices"></a>Bewährte Methoden für die Einwilligung:

Zusätzlich zu den oben beschriebenen Anforderungen für Nachrichten kann es ratsam sein, einige gängige bewährte Methoden zu implementieren, z. B.: 

- Ausführliche Informationen per Handlungsaufforderung. Geben Sie Folgendes an, um sicherzustellen, dass beim Einholen der Einwilligung alles korrekt verläuft:
  - Name oder Beschreibung Ihres Messagingprogramms
  - Nummern, von denen Empfänger Nachrichten erhalten 
  - Alle geltenden Geschäftsbedingungen (bevor eine Person die Einwilligung für den Empfang Ihrer Nachrichten erteilt)
- Genaue Nachweise der Einwilligung. Sie sollten die Nachweise für alle Einwilligungen, die eine Person Ihnen erteilt, mindestens vier Jahre lang aufbewahren. Die Nachweise zur Einwilligung können Folgendes umfassen:
  - Zeitstempel
  - Medium, über das die Einwilligung eingeholt wurde
  - Kampagne, für die die Einwilligung eingeholt wurde
  - Bildschirmaufnahmen
  - Sitzungs-ID oder IP-Adresse der Person, die die Einwilligung erteilt hat
- Richtlinien zu Sicherheit und Datenschutz. Die Empfehlung für Entwickler lautet, eindeutige Datenschutzrichtlinien bereitzustellen, die von Nachrichtenempfängern vor dem Erteilen der Einwilligung gelesen werden können. Darüber hinaus empfehlen wir Ihnen, proaktive Sicherheitskontrollen einzurichten, um die privaten Informationen der Personen zu schützen.


## <a name="double-opt-in-consent"></a>Doppelte Einwilligung für die Teilnahme:

Die Empfehlung für Azure Communication Services lautet, für alle Nachrichtenkampagnen die doppelte Einwilligung für die Teilnahme zu nutzen. Die doppelte Einwilligung ist ein zweistufiger Prozess, bei dem eine Person zunächst die Einwilligung erteilt, bestimmte Arten von Nachrichten von Ihnen zu erhalten. Anschließend senden Sie eine weitere Nachricht, um sich die Einwilligung bestätigen zu lassen. Sie sollten weitere Nachrichten erst dann senden, nachdem der Nachrichtenempfänger seine Einwilligung bestätigt hat.

Die von Ihnen gesendete anfängliche Bestätigungsmeldung sollte die folgenden Informationen enthalten: Ihre Identität, die Option zum Beenden der Teilnahme (z. B. Befehl „STOPP“), eine gebührenfreie Telefonnummer oder Befehl „HILFE“ zum Anfordern weiterer Informationen, Benachrichtigung über die Registrierung der Person für ein Programm mit regelmäßigen Nachrichten, eine kurze Beschreibung des Programms, Häufigkeit der gesendeten Nachrichten und berechnete Gebühren (falls zutreffend). 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Ist für Azure Communication Services die Nutzung der doppelten Einwilligung für die Teilnahme erforderlich?
Ja. Die Nutzung der doppelten Einwilligung für die Teilnahme ist generell zu empfehlen. In Azure Communication Services ist die doppelte Einwilligung für die Teilnahme für einige Arten von Nachrichtenkampagnen zudem obligatorisch, da diese häufig für Phishingangriffe genutzt werden oder zu Beschwerden von Kunden führen können. Beispiele für Kampagnen dieser Art sind:
- Nachrichten zur KFZ-Garantie
- Krankenversicherungsangebote mit kurzer Laufzeit
- Nachrichten zur Schuldenfinanzierung oder Reduzierung von Zinsraten, wenn diese nicht von einem Finanzinstitut stammen
- Nachrichten zur Leadgenerierung
- Gewinnspiele, Preisausschreiben und Werbegeschenke
- Angebote für Heimarbeit

Die Kampagnen, für die eine doppelte Einwilligung für die Teilnahme obligatorisch ist, können sich unter Azure Communication Services jederzeit ändern.

### <a name="exceptions-to-traditional-consent-rules"></a>Ausnahmen von herkömmlichen Einwilligungsregeln:
Vor dem Senden einer Nachricht ist normalerweise eine ausdrückliche Einwilligung erforderlich, aber es gibt zwei Fälle, in denen die Einwilligung einer Person für den Erhalt von Nachrichten als implizit vorhanden angesehen werden kann.

- Der Empfänger beginnt einen Nachrichtenaustausch. Wenn eine Person einen Nachrichtenaustausch initiiert, indem sie Ihnen eine Nachricht sendet, dürfen Sie relevante Informationen als Antwort auf eine bestimmte Nach- bzw. Anfrage senden, die in der Nachricht enthalten ist. Die von der Person implizit erteilte Einwilligung ist hierbei aber auf den Austausch beschränkt, der von der Person begonnen wurde, sofern Sie keine Einwilligung für einen weiter gehenden Austausch erhalten.
- Ausnahmen für bestimmte Dienstleistungen: Es gibt mehrere spezifische Dienstleistungen, bei denen Sie über eine implizite Einwilligung für das Senden einer Nachricht verfügen. Die häufigsten sind: 
- Nachrichten zur Lieferung eines Pakets
- Nachrichten von Finanzinstituten, bei denen der Faktor Zeit eine wichtige Rolle spielt (z. B. potenziell betrügerische Transaktionen oder Datenpannen)
- Nachrichten im Gesundheitswesen, bei denen der Faktor Zeit eine wichtige Rolle spielt und die Behandlungszwecken dienen (z. B. Erinnerungen zu Terminen oder Untersuchungen, Laborergebnisse und Benachrichtigungen zu Verschreibungen) 
 
Alle diese Nachrichten dürfen keine Informationen zur Kundenwerbung oder andere Werbung enthalten.


## <a name="opt-out"></a>Beendigung der Teilnahme

Nachrichtenempfänger können alle geeigneten Mittel nutzen, um ihre Einwilligung zu widerrufen und den Erhalt weiterer Nachrichten zu beenden. Es ist Ihnen nicht gestattet, ein ausschließliches Mittel festzulegen, das von Nachrichtenempfängern zum Widerrufen ihrer Einwilligung verwendet werden muss. 

### <a name="opt-out-requirements"></a>Anforderungen für die Beendigung der Teilnahme:

Stellen Sie sicher, dass Nachrichtenempfänger jederzeit die Möglichkeit haben, den Erhalt weiterer Nachrichten zu beenden. Darüber hinaus müssen Sie mehrere Optionen für die Beendigung der Teilnahme bereitstellen. Nachdem ein Nachrichtenempfänger die Teilnahme beendet hat, sollten Sie keine weiteren Nachrichten senden, sofern die Person nicht erneut eine entsprechende Einwilligung erteilt.

Eine der häufigsten Vorgehensweisen für die Beendigung der Teilnahme ist das Einfügen des Schlüsselworts „STOPP“ in die erste Nachricht eines neuen Austauschs. Sie sollten darauf vorbereitet sein, für Kunden die Beendigung durchzuführen, die mit dem Wort „stopp“ (in Kleinbuchstaben) oder anderen häufig verwendeten Wörtern wie „abbestellen“ oder „kündigen“ antworten. Nachdem eine Person ihre Einwilligung widerrufen hat, sollten Sie sie aus allen Kampagnen mit regelmäßig gesendeten Nachrichten entfernen, sofern diese Person nicht ausdrücklich angibt, dass sie die Nachrichten eines bestimmten Programms weiterhin erhalten möchte.

### <a name="opt-out-best-practices"></a>Bewährte Methoden für die Beendigung der Teilnahme:

Neben Schlüsselwörtern bestehen andere Möglichkeiten zur Beendigung der Teilnahme beispielsweise darin, eine entsprechende E-Mail-Adresse, eine Telefonnummer des Kundensupports oder einen Link auf Ihrer Webseite für Kunden bereitzustellen. 


### <a name="how-we-handle-opt-out-requests"></a>Verarbeitung von Anforderungen zur Beendigung der Teilnahme:

Wenn eine Person über eine gebührenfreie Azure Communication Services-Telefonnummer die Beendigung der Teilnahme anfordert, wird das Senden aller weiteren Nachrichten von dieser Nummer automatisch beendet. Sie müssen aber trotzdem sicherstellen, dass Sie im Rahmen der Nachrichtenkampagne keine weiteren Nachrichten von neuen oder anderen Nummern aus senden. Falls Sie auf anderem Wege die ausdrückliche Einwilligung für eine andere Nachrichtenkampagne erhalten haben, können Sie über die andere Nummer der entsprechenden Kampagne weiterhin Nachrichten senden. Weitere Informationen zur Verarbeitung von Anforderungen zur Beendigung der Teilnahme finden Sie auf der [Seite mit den häufig gestellten Fragen](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services).

## <a name="message-content"></a>Nachrichteninhalt

### <a name="adult-content"></a>Jugendgefährdender Inhalt:

Falls Nachrichten Elemente aus den Bereichen Sex, Hass, Alkohol, Waffen, Tabak, Glücksspiel, Gewinnspiele oder Preisausschreiben enthalten, müssen ggf. weitere Anforderungen erfüllt werden. In einigen Rechtsräumen ist die Verwendung dieser Art von Inhalt ausdrücklich verboten. Wenn Sie eine Nachricht mit diesem Inhalt senden, sind Sie dazu verpflichtet, alle geltenden Gesetze in den Rechtsräumen einzuhalten, in denen die Nachricht empfangen wird. Auf Anforderung von Strafverfolgungsbehörden oder von Azure Communication Services müssen Sie nachweisen können, dass Sie die vor Ort geltenden Gesetze einhalten, mit denen die Verwendung von jugendgefährdendem Inhalt jeweils geregelt wird.

Auch wenn mit Inhalt dieser Art nicht gegen Gesetze verstoßen wird, sollten Sie bei der Registrierung für die Teilnahme ein Verfahren zur Altersüberprüfung einbauen, um zu verifizieren, ob der jeweilige Nachrichtenempfänger alt genug für den Erhalt von jugendgefährdendem Inhalt ist. In den USA gelten besondere rechtliche Anforderungen für Marketingnachrichten, die für Kinder unter 13 Jahren bestimmt sind. 

### <a name="prohibited-practices"></a>Unzulässige Vorgehensweisen:

Weder Sie noch Ihre Kunden dürfen Azure Communication Services verwenden, um angemessene Abmeldeanfragen zu umgehen. Darüber hinaus dürfen Sie und Ihre Kunden keine Maßnahmen umgehen, die von Azure Communication Services oder einem Kommunikationsdienstanbieter implementiert wurden, um die Einhaltung von Messaginganforderungen und Branchenstandards sicherzustellen.

Unabhängig von der Erteilung einer Einwilligung sind auch bestimmte Nachrichteninhalte unter Azure Communication Services verboten. Folgende Inhalte sind verboten:
- Inhalte, mit denen ungesetzliche Aktivitäten gefördert werden (in den USA z. B. Steuerhinterziehung oder Tierquälerei)
- Hass, Verleumdungen, Belästigungen oder andere offenkundig verletzende Inhalte
- Pornografische Inhalte
- Obszöne oder vulgäre Inhalte
- Einschüchterungen und Bedrohungen
- Inhalte, die dazu dienen, zu unterschlagen, zu täuschen, Verletzungen zu verursachen oder sich auf unberechtigte Weise zu bereichern 
- Inhalte, die Verletzungen, Diskriminierungen oder Gewalt nach sich ziehen
- Inhalte, durch die Schadsoftware verbreitet wird
- Inhalte, mit denen Anforderungen zur Altersüberprüfung umgangen werden sollen

Wir behalten uns das Recht vor, die Liste mit den unerlaubten Inhalten jederzeit zu ändern.

## <a name="spoofing"></a>Spoofing

Beim Spoofing wird eine irreführende oder falsche Absendernummer auf dem Gerät eines Nachrichtenempfängers angezeigt. Wir empfehlen Ihnen dringend, keine Nachrichten zu senden, die Spoofing enthalten. Dies gilt für Sie selbst und für von Ihnen genutzte Dienstanbieter. Beim Spoofing wird die Identität des Absenders verschleiert und verhindert, dass Nachrichtenempfänger den Erhalt von unerwünschten Nachrichten auf einfache Weise ablehnen können. Außerdem ist es erforderlich, dass Sie alle geltenden Gesetze befolgen, die das Spoofing betreffen.

## <a name="final-thoughts"></a>Schlussbemerkungen

### <a name="legal-responsibility"></a>Rechtliche Verantwortung:

Diese Nachrichtenrichtlinie stellt keine rechtliche Beratung dar, und wir behalten uns das Recht vor, die Richtlinie jederzeit zu ändern. Azure Communication Services ist nicht dafür verantwortlich sicherzustellen, dass für die Inhalte, die Sendezeitpunkte oder die Empfänger der Nachrichten unserer Kunden alle geltenden rechtlichen Anforderungen erfüllt sind. 

Unsere Kunden sind selbst dafür verantwortlich, alle Anforderungen zu erfüllen, die für das Senden von Nachrichten gelten. Wenn Sie Azure Communication Services als Plattform- oder Softwareanbieter zum Senden von Nachrichten nutzen, sollten Sie es auch für Ihre Kunden obligatorisch machen, dass alle in dieser Nachrichtenrichtlinie beschriebenen Anforderungen erfüllt werden. Der CTIA-Artikel [Messaging Principles and Best Practices ](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf) (Messagingprinzipien und Best Practices) bietet eine hilfreiche Übersicht über die relevanten Branchenstandards.

### <a name="penalties"></a>Strafzahlungen:

Wir empfehlen unseren Kunden, Richtlinien und Prozeduren zu entwickeln und zu implementieren, mit denen sichergestellt wird, dass alle Anforderungen in Bezug auf das Senden von Nachrichten erfüllt werden. Verstöße gegen diese Anforderungen können zu hohen Strafzahlungen führen, die schnell ausufern können. Es liegt in Ihrem ureigensten Interesse, sich mit allen geltenden Anforderungen in Bezug auf das Senden von Nachrichten vertraut zu machen und diese zu erfüllen und wirksame Schutzmaßnahmen zu ergreifen, mit denen Verstöße rechtzeitig eingedämmt und korrigiert werden können. Falls Sie gegen unsere Nachrichtenrichtlinie oder andere rechtliche Anforderungen verstoßen, unterstützen wir Sie dabei, die erforderliche Konformität zu erzielen. Wir behalten uns aber das Recht vor, jeden Kunden von der Azure Communication Services-Plattform auszuschließen, der unsere Nachrichtenrichtlinie oder die rechtlichen Anforderungen nicht einhält.
