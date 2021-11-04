---
title: Referenz zur Azure CDN-Regel-Engine | Microsoft-Dokumentation
description: Referenzdokumentation zu den Übereinstimmungsbedingungen und Features der Azure CDN-Regel-Engine.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: duau
ms.openlocfilehash: 928e3fb96cd1acdbbfe7aceea5f5ab9ceca97909
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461412"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referenz zur Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel enthält ausführliche Beschreibungen der verfügbaren Übereinstimmungsbedingungen und Features für die [Regel-Engine](cdn-verizon-premium-rules-engine.md) des Azure Content Delivery Network (CDN).

Die Regel-Engine fungiert als endgültige Autorität, wie vom CDN bestimmte Typen von Anforderungen verarbeitet werden.

**Allgemeine Zwecke**:

- Außerkraftsetzen oder Definieren einer benutzerdefinierten Cacherichtlinie
- Schützen oder Ablehnen von Anforderungen vertraulicher Inhalte
- Umleiten von Anforderungen
- Speichern benutzerdefinierter Protokolldaten
## <a name="key-concepts"></a>Wichtige Begriffe
Im Folgenden werden die wichtigsten Konzepte für das Einrichten der Regel-Engine beschrieben.
### <a name="draft"></a>Entwurf
Ein Entwurf einer Richtlinie besteht aus mindestens einer Regel, die zum Identifizieren von Anforderungen und der Aktionen verwendet wird, die auf sie angewendet werden. Ein Entwurf ist eine fortlaufende Arbeit, die häufige Konfigurationsaktualisierungen ermöglicht, ohne dass sich dies auf den Websitedatenverkehr auswirkt. Zum Fertigstellen eines Entwurfs sollte dieser in eine schreibgeschützte Richtlinie konvertiert werden.

### <a name="rule"></a>Regel
Eine Regel identifiziert einen oder mehrere Typen von Anforderungen und den Satz von Aktionen, die auf sie angewendet werden.

Sie besteht aus: 

- Einem Satz bedingter Ausdrücke, die die Logik zum Identifizieren von Anforderungen definieren.
- Einer Reihe von Übereinstimmungsbedingungen, die die Kriterien zum Identifizieren von Anforderungen definieren.
- Einer Reihe von Features, die definieren, wie das CDN die oben genannten Anforderungen behandelt.
Diese Elemente sind in der folgenden Abbildung gekennzeichnet.

![Screenshot mit Bezeichnungen: Der bedingte Ausdruck, die Übereinstimmung und die Funktionen einer Regel](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Richtlinie
Eine Richtlinie, die aus einem Satz schreibgeschützter Regeln besteht, ermöglicht Folgendes:

- Erstellen, Speichern und Verwalten mehrerer Varianten der Regeln
- Zurücksetzen auf eine zuvor bereitgestellte Version
- Erstellen ereignisspezifischer Regeln im Voraus (z. B. einer Regel, die den Datenverkehr aufgrund einer Kundenursprungswartung umleitet)

> [!NOTE]
> Obwohl nur eine einzige Richtlinie pro Umgebung zulässig ist, können Richtlinien bei Bedarf bereitgestellt werden.

### <a name="deploy-request"></a>Bereitstellungsanforderung
Eine Bereitstellungsanforderung bietet ein einfaches und optimiertes Verfahren für die schnelle Anwendung einer Richtlinie auf die Staging- oder Produktionsumgebung. Ein Verlauf der Bereitstellungsanforderungen wird bereitgestellt, um die Nachverfolgung der auf diese Umgebungen angewendeten Änderungen zu erleichtern.

> [!NOTE]
> Nur Anforderungen, die von unserem automatisierten Überprüfungs- und Fehlererkennungssystem nicht akzeptiert werden, erfordern eine manuelle Überprüfung und Genehmigung.

### <a name="rule-precedence"></a>Rangfolge der Regeln
Die Regeln in einer Richtlinie werden normalerweise in der Reihenfolge verarbeitet, in der sie aufgelistet sind (d. h. von oben nach unten). Wenn die Anforderung mit Regeln übereinstimmt, die miteinander in Konflikt stehen, hat die letzte zu verarbeitende Regel Vorrang.

### <a name="policy-deployment-workflow"></a>Workflow der Richtlinienbereitstellung
Der Workflow für das Anwenden einer Richtlinie auf die Produktions- oder Stagingumgebung ist unten dargestellt.

![Workflow der Richtlinienbereitstellung](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Schritt |BESCHREIBUNG |
|---------|---------|
|[Erstellen des Entwurfs](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Ein Entwurf besteht aus einer Reihe von Regeln, die definieren, wie Anforderungen für Ihre Inhalte vom CDN verarbeitet werden sollen.     |
|Sperren des Entwurfs   |     Nach dem Abschließen eines Entwurfs sollte dieser gesperrt und in eine schreibgeschützte Richtlinie konvertiert werden.    |
|[Übermitteln der Bereitstellungsanforderung](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Eine Bereitstellungsanforderung ermöglicht das Anwenden einer Richtlinie auf den Test- oder Produktionsdatenverkehr.</br> <br>Übermitteln Sie eine Bereitstellungsanforderung an die Staging- oder Produktionsumgebung.</br>     |
|Überprüfen der Bereitstellungsanforderung   |    <br>Eine Bereitstellungsanforderung wird einer automatisierten Überprüfung und Fehlererkennung unterzogen.</br><br>Obwohl die meisten Bereitstellungsanforderungen automatisch genehmigt werden, erfordern komplexere Richtlinien eine manuelle Überprüfung.</br>   |
|Richtlinienbereitstellung ([Staging](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Nach der Genehmigung einer Bereitstellungsanforderung an die Stagingumgebung wird eine Richtlinie auf die Stagingumgebung angewendet. Diese Umgebung ermöglicht das erneute Testen der Richtlinie mit simuliertem Websitedatenverkehr.</br><br>Sobald die Richtlinie auf Live-Websitedatenverkehr angewendet werden kann, sollte eine neue Bereitstellungsanforderung für die Produktionsumgebung übermittelt werden.</br>      |
|Richtlinienbereitstellung ([Produktion](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Nach der Genehmigung einer Bereitstellungsanforderung an die Produktionsumgebung wird eine Richtlinie auf die Produktionsumgebung angewendet. In dieser Umgebung kann eine Richtlinie als endgültige Stelle fungieren, die bestimmt, wie die Verarbeitung von Livedatenverkehr durch das CDN erfolgen soll.     |
## <a name="syntax"></a>Syntax

Die Art und Weise, in der Sonderzeichen behandelt werden, hängt davon ab, wie eine Übereinstimmungsbedingung Textwerte behandelt. Eine Übereinstimmungsbedingung (bzw. Feature) kann Text interpretieren als:

- [**Literalwerte**](#literal-values)
- [**Platzhalterwerte**](#wildcard-values)
- [**Reguläre Ausdrücke**](#regular-expressions)

### <a name="literal-values"></a>Literalwerte

Text, der als Literalwert interpretiert wird, behandelt alle Sonderzeichen (mit Ausnahme des Zeichens „%“) als Teil des Werts, für den eine Übereinstimmung gefunden werden muss. Eine Literalübereinstimmungsbedingung, die auf `\'*'\` festgelegt ist, wird demnach nur erfüllt, wenn exakt dieser Wert (d.h. `\'*'\`) gefunden wird.

Ein Prozentzeichen wird zum Angeben der URL-Codierung verwendet (z.B. `%20`).

### <a name="wildcard-values"></a>Platzhalterwerte

Text, der als Platzhalterwert interpretiert wird, weist Sonderzeichen eine zusätzliche Bedeutung zu. In der folgenden Tabelle wird beschrieben, wie die folgende Gruppe von Zeichen interpretiert wird:

Zeichen | BESCHREIBUNG
----------|------------
\ | Ein umgekehrter Schrägstrich wird als Escapezeichen für die in dieser Tabelle angegebenen Zeichen verwendet. Direkt vor dem Sonderzeichen, das mit Escapezeichen versehen werden soll, muss ein umgekehrter Schrägstrich eingegeben werden.<br/>Es folgt als Beispiel die Syntax für das Escapezeichen eines Sternchens: `\*`
% | Ein Prozentzeichen wird zum Angeben der URL-Codierung verwendet (z.B. `%20`).
\* | Ein Sternchen ist ein Platzhalter, der ein oder mehrere Zeichen darstellt.
LeerZchn | Ein Leerzeichen gibt an, dass eine Übereinstimmungsbedingung entweder mit den angegebenen Werten oder Mustern erfüllt werden kann.
'Wert' | Ein einfaches Anführungszeichen hat keine besondere Bedeutung. Jedoch wird eine Gruppe einfacher Anführungszeichen verwendet, um anzugeben, dass ein Wert als Literalwert behandelt werden soll. Es kann auf folgende Weisen verwendet werden:<br><br/>– Es ermöglicht das Erfüllen einer Übereinstimmungsbedingung, sobald der angegebene Wert mit einem beliebigen Teil des Vergleichswerts übereinstimmt.  Beispielsweise stimmt `'ma'` mit beliebigen der folgenden Zeichenfolgen überein: <br/><br/>/business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template.**ma** p<br /><br />– Es ermöglicht die Angabe eines Sonderzeichens als Literalzeichen. Sie können z.B. ein literales Leerzeichen angeben, indem Sie ein Leerzeichen in einfache Anführungszeichen setzen (d.h. `' '` oder `'sample value'`).<br/>– Es ermöglicht die Angabe eines leeren Werts. Geben Sie einen leeren Wert an, indem Sie zwei einfache Anführungszeichen eingeben (d.h. '').<br /><br/>**Wichtig:**<br/>– Wenn der angegebene Wert keinen Platzhalter enthält, wird er automatisch als Literalwert behandelt, d.h., es müssen nicht zwei einfache Anführungszeichen angegeben werden.<br/>– Wenn ein umgekehrter Schrägstrich nicht als Escapezeichen für ein anderes Zeichen in dieser Tabelle fungiert, wird er ignoriert, sofern er zwischen zwei einfachen Anführungszeichen angegeben wird.<br/>– Eine andere Möglichkeit zum Angeben eines Sonderzeichens als Literalzeichen besteht darin, es mithilfe eines umgekehrten Schrägstrichs (d.h. `\`) mit einem Escapezeichen zu versehen.

### <a name="regular-expressions"></a>Reguläre Ausdrücke

Reguläre Ausdrücke definieren ein Muster, das in einem Textwert gesucht wird. Die Notation regulärer Ausdrücke definiert spezifische Bedeutungen für eine Vielzahl von Symbolen. Die folgende Tabelle zeigt, wie Sonderzeichen von Übereinstimmungsbedingungen behandelt werden, die reguläre Ausdrücke unterstützen.

Sonderzeichen | BESCHREIBUNG
------------------|------------
\ | Ein umgekehrter Schrägstrich als Escapezeichen für das nach diesem folgende Zeichen bewirkt, dass das Zeichen als Literalwert behandelt wird, anstatt seine Bedeutung als regulärer Ausdruck zu haben. Es folgt als Beispiel die Syntax für das Escapezeichen eines Sternchens: `\*`
% | Die Bedeutung eines Prozentzeichens hängt von seiner Verwendung ab.<br/><br/> `%{HTTPVariable}`: Diese Syntax gibt eine HTTP-Variable an.<br/>`%{HTTPVariable%Pattern}`: Diese Syntax verwendet ein Prozentzeichen, um eine HTTP-Variable und ein Trennzeichen anzugeben.<br />`\%`: Wenn ein Prozentzeichen mit einem Escapezeichen versehen wird, kann es als Literalwert verwendet werden oder die URL-Codierung angeben (z. B. `\%20`).
\* | Ein Sternchen ermöglicht, dass für das vorherige Zeichen keine oder mehrere Übereinstimmungen gefunden werden.
LeerZchn | Ein Leerzeichen wird in der Regel als Literalzeichen behandelt.
'Wert' | Einfache Anführungszeichen werden als Literalzeichen behandelt. Eine Gruppe einfacher Anführungszeichen hat keine besondere Bedeutung.

Übereinstimmungsbedingungen und Features, die reguläre Ausdrücke unterstützen, akzeptieren Muster, die durch PCRE (Perl Compatible Regular Expressions) definiert sind.



## <a name="next-steps"></a>Nächste Schritte

- [Übereinstimmungsbedingungen der Regel-Engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regel-Engine – bedingte Ausdrücke](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regel-Engine – Features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Überschreiben des HTTP-Verhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)
- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
