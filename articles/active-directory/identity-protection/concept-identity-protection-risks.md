---
title: Was bedeutet Risiko? Azure AD Identity Protection
description: Erläutern von Risikoereignissen in Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c783ec82455e1460e3508357c29167c40ca4e9db
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307970"
---
# <a name="what-is-risk"></a>Was bedeutet Risiko?

Risikoerkennungen in Azure AD Identity Protection umfassen alle identifizierten verdächtigen Aktionen im Zusammenhang mit Benutzerkonten im Verzeichnis. Risikoerkennungen (im Zusammenhang mit Benutzern und Anmeldungen) tragen zur Gesamtrisikobewertung des Benutzers bei, die im Bericht zu riskanten Benutzern enthalten ist.

Identity Protection bietet Organisationen Zugriff auf leistungsstarke Ressourcen, um diese verdächtigen Aktionen zu erkennen und schnell darauf zu reagieren. 

![Sicherheitsübersicht riskanter Benutzer und Anmeldungen](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection generiert Risikoerkennungen nur, wenn die richtigen Anmeldeinformationen verwendet werden. Wenn bei der Anmeldung falsche Anmeldeinformationen verwendet werden, stellt dies kein Risiko durch eine Gefährdung der Anmeldeinformationen dar.

## <a name="risk-types-and-detection"></a>Risikotypen und Erkennung

Risiken können auf Ebene der **Benutzer** und der **Anmeldungen** erkannt werden, und es gibt zwei Arten der Erkennung oder Berechnung (**Echtzeit** und **Offline**).

Echtzeit-Erkennungen werden möglicherweise erst nach fünf bis 10 Minuten in den Berichten angezeigt. Offline-Erkennungen werden unter Umständen erst nach 48 Stunden in der Berichterstattung angezeigt.

> [!NOTE] 
> Unser System erkennt möglicherweise, dass das Risikoereignis, das zur Risikobewertung „Risikobenutzer“ beigetragen hat, falsch positiv war oder das Benutzerrisiko durch die Richtliniendurchsetzung behoben wurde, wie z. B. das Abschließen einer MFA-Eingabeaufforderung oder die Änderung in ein sicheres Kennwort. Aus diesem Grund wird der Risikostatus von unserem System verworfen, und es wird ein Risikodetail angezeigt, dass die KI die Anmeldung als sicher bestätigt, sodass dies nicht mehr zum Risiko des Benutzers beiträgt. 

### <a name="user-linked-detections"></a>Mit dem Benutzer verknüpfte Erkennungen

Bei einem Benutzer können riskante Aktivitäten erkannt werden, die nicht mit einer bestimmten bösartigen Anmeldung, sondern mit dem Benutzer selbst verbunden sind. Diese Risikoerkennungen werden offline anhand interner und externer Bedrohungsdatenquellen von Microsoft berechnet: z.B. Sicherheitsforscher, Strafverfolgungsexperten, Sicherheitsteams von Microsoft und andere vertrauenswürdige Quellen.

Diese Risiken werden offline anhand interner und externer Threat Intelligence-Quellen von Microsoft berechnet, z. B. Sicherheitsexperten, Strafverfolgungsbehörden, Sicherheitsteams bei Microsoft und anderen vertrauenswürdigen Quellen.

| Risikoerkennung | BESCHREIBUNG |
| --- | --- |
| Kompromittierte Anmeldeinformationen | Dieser Risikoerkennungstyp gibt an, dass die gültigen Anmeldeinformationen des Benutzers kompromittiert wurden. Wenn Internetkriminelle an gültige Kennwörter von berechtigten Benutzern gelangen, geben sie diese Anmeldeinformationen häufig weiter. Diese Freigabe erfolgt in der Regel durch eine Veröffentlichung im Darknet oder auf Paste Sites oder durch den Handel und Verkauf der Anmeldeinformationen auf dem Schwarzmarkt. Wenn der Microsoft-Dienst für durchgesickerte Anmeldedaten Benutzeranmeldedaten aus dem Dark Web, von Paste-Sites oder anderen Quellen erhält, werden diese mit den aktuellen gültigen Anmeldedaten der Azure AD-Benutzer abgeglichen, um gültige Übereinstimmungen zu finden. Weitere Informationen zu kompromittierten Anmeldeinformationen finden Sie unter [Häufig gestellte Fragen](#common-questions). |
| Azure AD Threat Intelligence | Dieser Risikoerkennungstyp gibt Benutzeraktivitäten an, die für den angegebenen Benutzer unüblich oder mit bekannten Angriffsmustern konsistent sind (basierend auf internen und externen Threat Intelligence-Quellen von Microsoft). |

### <a name="sign-in-risk"></a>Anmelderisiko

Ein Anmelderisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Authentifizierungsanforderung vom Identitätsbesitzer nicht autorisiert wurde. 

Diese Risiken können in Echtzeit oder offline anhand interner und externer Threat Intelligence-Quellen von Microsoft berechnet werden, z. B. Sicherheitsexperten, Strafverfolgungsbehörden, Sicherheitsteams bei Microsoft und anderen vertrauenswürdigen Quellen.

| Risikoerkennung | Erkennungstyp | BESCHREIBUNG |
| --- | --- | --- |
| Anonyme IP-Adresse | Echtzeit | Dieser Risikoerkennungstyp gibt Anmeldungen über eine anonyme IP-Adresse (z.B. Tor-Browser oder ein anonymisiertes VPN) an. Diese IP-Adressen werden in der Regel von Akteuren verwendet, die ihre Anmeldetelemetrie (IP-Adresse, Standort, Gerät usw.) für mögliche böswillige Absichten verbergen wollen. |
| Ungewöhnlicher Ortswechsel | Offline | Mit diesem Risikoerkennungstyp werden zwei Anmeldungen identifiziert, die von weit entfernten Orten durchgeführt wurden und bei denen mindestens einer der Orte aufgrund des bisherigen Verhaltens atypisch für den Benutzer ist. Neben zahlreichen anderen Faktoren berücksichtigt dieser Machine Learning-Algorithmus die Zeit zwischen den beiden Anmeldungen sowie die Zeit, die der Benutzer für einen Ortswechsel benötigen würde, wovon sich ableiten lässt, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet. <br><br> Bei diesem Algorithmus werden offensichtliche falsch positive Ergebnisse ignoriert, die zu den unmöglichen Ortswechselbedingungen beitragen. Hierzu zählen etwa VPNs und regelmäßig von anderen Benutzern der Organisation verwendete Standorte. Das System verfügt über einen anfänglichen Lernzeitraum von 14 Tagen oder 10 Anmeldungen (je nachdem, welcher Punkt früher erreicht wird), in dem das Anmeldeverhalten des neuen Benutzers erlernt wird. |
| Anomales Token | Offline | Diese Erkennung deutet darauf hin, dass der Token ungewöhnliche Merkmale aufweist, z.B. eine ungewöhnliche Gültigkeitsdauer oder einen Token, der von einem unbekannten Ort aus gespielt wird. Diese Erkennung deckt Sitzungstoken und Aktualisierungstoken ab. |
| Anomaler Tokenaussteller | Offline |Diese Risikoerkennung gibt an, dass der SAML-Tokenaussteller für das zugeordnete SAML-Token möglicherweise kompromittiert ist. Die im Token enthaltenen Ansprüche sind ungewöhnlich oder stimmen mit bekannten Angriffsmustern überein. |
| Mit Schadsoftware verknüpfte IP-Adresse | Offline | Mit diesem Risikoerkennungstyp werden Anmeldungen von IP-Adressen identifiziert, die mit Schadsoftware infiziert sind und bekanntermaßen aktiv mit einem Botserver kommunizieren. Diese Erkennung wird ermittelt, indem IP-Adressen des Benutzergeräts mit IP-Adressen korreliert werden, die in Kontakt mit einem Botserver gestanden haben, während der Botserver aktiv war. <br><br> **[Diese Erkennung ist veraltet](../fundamentals/whats-new.md#planned-deprecation---malware-linked-ip-address-detection-in-identity-protection)** . Identity Protection generiert keine neuen Erkennungen des Typs „Mit Schadsoftware verknüpfte IP-Adresse“ mehr. Kunden, die derzeit Erkennungen des Typs „Mit Schadsoftware verknüpfte IP-Adresse“ in ihrem Mandanten haben, können diese bis zum Erreichen der 90-tägigen Aufbewahrungsdauer für Erkennungen weiterhin anzeigen, bereinigen oder verwerfen.|
| Verdächtiger Browser | Offline | Die Erkennung „Verdächtiger Browser“ weist auf ein anomales Verhalten hin, das auf verdächtigen Anmeldeaktivitäten mehrerer Mandanten aus verschiedenen Ländern im selben Browser beruht. |
| Ungewöhnliche Anmeldeeigenschaften | Echtzeit | Dieser Risikoerkennungstyp berücksichtigt den bisherigen Anmeldeverlauf (IP-Adresse, Breitengrad/Längengrad und ASN), um nach anomalen Anmeldungen zu suchen. Im System werden Informationen zu den vorherigen Standorten gespeichert, die von einem Benutzer genutzt wurden, und diese werden als „vertraute“ Standorte angesehen. Die Risikoerkennung wird ausgelöst, wenn die Anmeldung von einem Standort aus erfolgt, der in der Liste der bekannten Standorte noch nicht enthalten ist. Neu angelegte Benutzer befinden sich eine Zeit lang im "Lernmodus", in dem die Erkennung unbekannter Anmeldeeigenschaften ausgeschaltet wird, während unsere Algorithmen das Verhalten des Benutzers lernen. Die Dauer des Lernmodus ist dynamisch und hängt davon ab, wie lange es dauert, bis der Algorithmus genügend Informationen über die Anmeldemuster des Benutzers gesammelt hat. Die Mindestdauer beträgt fünf Tage. Ein Benutzer kann nach einer langen Zeit der Inaktivität erneut in den Lernmodus wechseln. Außerdem ignoriert das System Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem bekannten Speicherort liegen. <br><br> Diese Erkennung wird auch für die Standardauthentifizierung (bzw. ältere Protokolle) ausgeführt. Da diese Protokolle nicht über moderne Eigenschaften wie die Client-ID verfügen, gibt es nur begrenzte Telemetriedaten, um Fehlalarme zu reduzieren. Wir empfehlen unseren Kunden, auf eine moderne Authentifizierung umzusteigen. <br><br> Unbekannte Anmeldungseigenschaften können sowohl bei interaktiven als auch bei nicht-interaktiven Anmeldungen erkannt werden. Wenn diese Erkennung bei nicht-interaktiven Anmeldungen festgestellt wird, sollte sie aufgrund des Risikos von Token-Wiederholungsangriffen besonders sorgfältig geprüft werden.  |
| Benutzergefährdung durch Administrator bestätigt | Offline | Diese Erkennung gibt an, dass ein Administrator auf der Benutzeroberfläche für riskante Benutzer oder mithilfe der riskyUsers-API die Option „Benutzergefährdung bestätigen“ ausgewählt hat. Überprüfen Sie den Risikoverlauf des Benutzers (auf der Benutzeroberfläche oder über die API), um zu überprüfen, welcher Administrator diese Benutzergefährdung bestätigt hat. |
| Schädliche IP-Adresse | Offline | Diese Erkennung gibt eine Anmeldung über eine schädliche IP-Adresse an. Eine IP-Adresse wird wegen hoher Fehlerraten aufgrund von ungültige Anmeldeinformationen, die von der IP-Adresse oder anderen IP-Zuverlässigkeitsquellen empfangen wurden, als schädlich eingestuft. |
| Verdächtige Regeln zur Posteingangsänderung | Offline | Diese Erkennung wird von [Microsoft Defender für Cloud Apps](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) erkannt. Diese Erkennung erstellt ein Profil Ihrer Umgebung und löst Warnungen aus, wenn verdächtige Regeln zum Löschen oder Verschieben von Nachrichten oder Ordnern für den Posteingang eines Benutzers festgelegt werden. Diese Erkennung kann darauf hinweisen, dass das Konto des Benutzers kompromittiert ist, dass Nachrichten absichtlich ausgeblendet werden und das Postfach zum Verteilen von Spam und Schadsoftware in Ihrer Organisation verwendet wird. |
| Kennwortspray | Offline | Bei einem Kennwortspray-Angriff werden mehrere Benutzernamen unter Verwendung gängiger Kennwörter im Rahmen eines koordinierten Brute-Force-Angriffs angegriffen, um nicht autorisierten Zugriff zu erhalten. Die Risikoerkennung wird ausgelöst, wenn ein Kennwortspray-Angriff erfolgt ist. |
| Unmöglicher Ortswechsel | Offline | Diese Erkennung wird von [Microsoft Defender für Cloud Apps](/cloud-app-security/anomaly-detection-policy#impossible-travel) erkannt. Diese Erkennung identifiziert zwei Benutzeraktivitäten (in einer einzelnen Sitzung oder in mehreren Sitzungen), die von unterschiedlichen geografischen Standorten stammen und in einem Zeitraum liegen, der kürzer ist als die Zeit, die der Benutzer benötigt, um von Standort A zu Standort B zu gelangen. Dies deutet darauf hin, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet. |
| Neues Land/neue Region | Offline | Diese Erkennung wird von [Microsoft Defender für Cloud Apps](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country) erkannt. Bei dieser Erkennungsmethode werden anhand von in der Vergangenheit verwendeten Aktivitätsstandorten neue und selten verwendete Standorte ermittelt. Die Anomalieerkennungsengine speichert Informationen zu Standorten, die Benutzer der Organisation in der Vergangenheit verwendet haben. |
| Aktivität über anonyme IP-Adresse | Offline | Diese Erkennung wird von [Microsoft Defender für Cloud Apps](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses) erkannt. Diese Erkennung stellt fest, ob Benutzer eine IP-Adresse verwendet haben, die als anonyme Proxy-IP-Adresse identifiziert wurde. |
| Verdächtige Weiterleitung des Posteingangs | Offline | Diese Erkennung wird von [Microsoft Defender für Cloud Apps](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding) erkannt. Durch diese Erkennungsmethode werden verdächtige Regeln zur E-Mail-Weiterleitung erkannt. Hierzu gehört beispielsweise die Erstellung einer Posteingangsregel, die eine Kopie aller E-Mails an eine externe Adresse weiterleitet. |
| Azure AD Threat Intelligence | Offline | Dieser Risikoerkennungstyp gibt Anmeldeaktivitäten an, die für den angegebenen Benutzer unüblich sind oder mit bekannten Angriffsmustern übereinstimmen (basierend auf internen und externen Threat Intelligence-Quellen von Microsoft). |

### <a name="other-risk-detections"></a>Andere Risikoerkennungen

| Risikoerkennung | Erkennungstyp | BESCHREIBUNG |
| --- | --- | --- |
| Zusätzliches Risiko erkannt | Echtzeit oder offline | Diese Erkennung gibt an, dass eine der oben genannten Premium-Erkennungen erkannt wurde. Da die Premium-Erkennungen nur für Azure AD Premium P2-Kunden sichtbar sind, werden sie für Kunden ohne Azure AD Premium P2-Lizenz als "zusätzliches Risiko erkannt" bezeichnet. |

## <a name="common-questions"></a>Häufig gestellte Fragen

### <a name="risk-levels"></a>Risikostufen

Mit Identity Protection werden Risiken in drei Stufen eingeteilt: niedrig, mittel und hoch. Wenn Sie [benutzerdefinierte Richtlinien für den Identitätsschutz](./concept-identity-protection-policies.md#custom-conditional-access-policy) konfigurieren, können Sie diese auch so konfigurieren, dass sie auf der Ebene **Kein Risiko** ausgelöst werden. Kein Risiko bedeutet, dass es keine aktiven Anzeichen dafür gibt, dass die Identität des Benutzers kompromittiert wurde.

Microsoft stellt zwar keine spezifischen Details zur Risikoberechnung bereit, aber wir sagen, dass jede Stufe ein höheres Maß an Sicherheit bietet, dass der Benutzer oder die Anmeldung kompromittiert ist. Beispielsweise sind einmalige ungewöhnliche Anmeldeeigenschaften eines Benutzers unter Umständen nicht so riskant wie kompromittierte Anmeldeinformationen eines anderen Benutzers.

### <a name="password-hash-synchronization"></a>Kennworthashsynchronisierung

Damit Risiken wie kompromittierte Anmeldeinformationen erkannt werden können, müssen Kennworthashes vorhanden sein. Weitere Informationen zur Kennworthashsynchronisierung finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="why-are-there-risk-detections-generated-for-disabled-user-accounts"></a>Warum werden Risikoerkennungen für deaktivierte Benutzerkonten generiert?
          
Deaktivierte Benutzerkonten können erneut aktiviert werden. Wenn die Anmeldeinformationen eines deaktivierten Kontos kompromittiert sind und das Konto erneut aktiviert wird, könnten böswillige Akteure diese Anmeldeinformationen verwenden, um Zugriff zu erhalten. Aus diesem Grund generiert Identity Protection für deaktivierte Benutzerkonten Risikoerkennungen für verdächtige Aktivitäten, um Kunden über eine potenzielle Kontokompromittierung zu informieren. Wenn ein Konto nicht mehr verwendet und auch nicht wieder aktiviert wird, sollten Kunden erwägen, es zu löschen, um eine Kompromittierung zu verhindern. Für gelöschte Konten werden keine Risikoerkennungen generiert.

### <a name="leaked-credentials"></a>Kompromittierte Anmeldeinformationen

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Wo findet Microsoft kompromittierte Anmeldeinformationen?

Microsoft findet kompromittierte Anmeldeinformationen an verschiedenen Stellen. Dazu zählen:

- Öffentliche Paste-Websites, z. B. pastebin.com und paste.ca, auf denen böswillige Akteure in der Regel solche Materialien posten. Diese Stelle ist der erste Zwischenstopp von böswilligen Akteuren auf der Jagd nach gestohlenen Anmeldeinformationen.
- Strafverfolgungsbehörden.
- Andere Gruppen bei Microsoft, die das Darknet durchforsten.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Warum werden mir keine kompromittierten Anmeldeinformationen angezeigt?

Kompromittierte Anmeldeinformationen werden jedes Mal, wenn Microsoft einen neuen, öffentlich verfügbaren Batch findet, verarbeitet. Angesichts der Sensibilität der Daten werden die kompromittierten Anmeldeinformationen kurz nach der Verarbeitung gelöscht. Für Ihren Mandanten werden nur neue kompromittierte Anmeldeinformationen, die nach dem Aktivieren der Kennworthashsynchronisierung gefunden wurden, verarbeitet. Eine Überprüfung anhand zuvor gefundener Anmeldeinformationspaare erfolgt nicht. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Seit einiger Zeit sind keine Risikoereignisse mit kompromittierten Anmeldeinformationen mehr angezeigt worden.

Wenn Ihnen keine Risikoereignisse mit kompromittierten Anmeldeinformationen angezeigt wurden, kann das folgende Ursachen haben:

- Sie haben keine Kennworthashsynchronisierung für Ihren Mandanten aktiviert.
- Microsoft hat keine kompromittierten Anmeldeinformationspaare gefunden, die Ihren Benutzern entsprechen.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Wie oft verarbeitet Microsoft neue Anmeldeinformationen?

Anmeldeinformationen werden sofort nach deren Auffinden verarbeitet, in der Regel in mehreren Batches pro Tag.

### <a name="locations"></a>Standorte

Der Standort wird bei der Risikoerkennung durch die IP-Adresssuche bestimmt.

## <a name="next-steps"></a>Nächste Schritte

- [Verfügbare Richtlinien zum Mindern von Risiken](concept-identity-protection-policies.md)
- [Sicherheitsübersicht](concept-identity-protection-security-overview.md)
