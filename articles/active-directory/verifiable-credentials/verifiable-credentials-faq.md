---
title: 'Häufig gestellte Fragen: überprüfbare Azure-Anmeldeinformationen (Vorschau)'
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu überprüfbaren Anmeldeinformationen.
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 760f90a766eca28e3d6cc009e3370192a9bd5285
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994801"
---
# <a name="frequently-asked-questions-faq-preview"></a>Häufig gestellte Fragen (Vorschau)

Diese Seite enthält häufig gestellte Fragen zu überprüfbaren Anmeldeinformationen und zur dezentralisierten Identität. Die Fragen sind in folgende Abschnitte unterteilt.

- [Vokabular und Grundlagen](#the-basics)
- [Konzeptionelle Fragen zur dezentralisierten Identität](#conceptual-questions)
- [Fragen zur Verwendung der Vorschau für verifizierbare Anmeldeinformationen](#using-the-preview)

> [!IMPORTANT]
> Überprüfbare Anmeldeinformationen für Azure Active Directory-befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Grundlagen

### <a name="what-is-a-did"></a>Was ist ein DID? 

Dezentralisierte Bezeichner (Decentralized Identifiers, DIDs) sind Bezeichner, mit denen der Zugriff auf Ressourcen gesichert, Anmeldeinformationen signiert und überprüft und der Austausch von Anwendungsdaten vereinfacht werden kann. Im Gegensatz zu herkömmlichen Benutzernamen und e-Mail-Adressen gehören DIDs einer Entität und werden von dieser gesteuert. Dies kann eine Person, ein Gerät oder ein Unternehmen sein. DIDs bestehen unabhängig von externen Organisationen oder vertrauenswürdigen Mittelsmännern. Dies wird in [der W3C-Spezifikation für dezentralisierte Bezeichner](https://www.w3.org/TR/did-core/) ausführlicher erläutert.

### <a name="why-do-we-need-a-did"></a>Warum benötigen wir einen DID?

Vertrauen im digitalen Raum setzt grundsätzlich voraus, dass Teilnehmer eigene Identitäten besitzen und steuern können, und diese Identität beginnt beim Bezeichner.
In einer Zeit, in der Systeme und zentralisierte Identitäten täglich Ziel von groß angelegten Sicherheitsverstößen und Attacken sind, stellt die Dezentralisierung von Identitäten eine wichtige Sicherheitsanforderung für Verbraucher und Unternehmen dar.
Personen mit eigenen, steuerbaren Identitäten können so überprüfbare Daten und Nachweise austauschen. Eine Umgebung mit dezentralen Anmeldeinformationen ermöglicht die Automatisierung vieler Geschäftsprozesse, die aktuell noch manuell und arbeitsintensiv ablaufen.

### <a name="what-is-a-verifiable-credential"></a>Was sind überprüfbare Anmeldeinformationen? 

Anmeldeinformationen oder Legitimationen gehören zu unserem Alltag: Anhand von Führerscheinen wird überprüft, ob wir ein Fahrzeug lenken dürfen, Universitätsabschlüsse belegen unseren Bildungsstand, und amtlich ausgestellte Pässe ermöglichen es uns, in andere Länder zu reisen. Überprüfbare Anmeldeinformationen ermöglichen es, diese Arten von Informationen Internet so einzusetzen, dass sie kryptografisch sicher sind, den Datenschutz gewährleisten und technisch überprüfbar sind. In [der Spezifikation für überprüfbare W3C-Anmeldeinformationen](https://www.w3.org/TR/vc-data-model//) wird dies ausführlich erläutert.


## <a name="conceptual-questions"></a>Konzeptionelle Fragen

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Was geschieht, wenn ein Benutzer sein Smartphone verliert? Können er seine Identität wiederherstellen?

Es gibt mehrere Möglichkeiten für Benutzer, ihre Identität wiederherzustellen, von denen jede gewisse Nachteile mit sich bringt. Wir evaluieren derzeit Optionen und entwerfen Ansätze für die Wiederherstellung, die Benutzerfreundlichkeit und Sicherheit bieten und dabei Datenschutz und Selbstbestimmung des Benutzers gewährleisten.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Wie weiß ein Benutzer, ob er einer Anfrage von einem Zertifikataussteller oder -verifizierer vertrauen kann? Woher weiß er, dass eine DID wirklich zu einem Unternehmen gehört?

Wir haben [die DID-Konfigurationsspezifikation der Decentralized Identity Foundation](https://identity.foundation/.well-known/resources/did-configuration/) implementiert, um eine DID mit einem allgemein bekannten System, Domain Names, zu verbinden. Jede DID, die mit überprüfbaren Anmeldeinformationen von Azure Active Directory erstellt wurde, kann einen Stammdomänennamen enthalten, der im DID-Dokument codiert wird. Weitere Informationen finden Sie im Artikel [Verknüpfen Ihrer Domäne mit Ihrem verteilten Bezeichner](how-to-dnsbind.md).  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Warum wird für die Vorschau der überprüfbaren Anmeldeinformationen ION als DID-Methode - und somit Bitcoin zur Bereitstellung der dezentralen Public Key Infrastruktur verwendet?

ION ist ein dezentralisiertes, genehmigungsfreies und skalierbares Layer 2-Netzwerk mit dezentralem Bezeichner, das auf Bitcoin läuft. Es ermöglicht Skalierbarkeit ohne ein spezielles Kryptoasset-Token, vertrauenswürdige Validatoren oder zentralisierte Konsensmechanismen. Wir verwenden Bitcoin für Layer 1 aufgrund der Stärke des dezentralisierten Netzwerks, um einen hohen Grad an Unveränderbarkeit für eine chronologische Ereigniserfassung zu ermöglichen.

## <a name="using-the-preview"></a>Verwendung der Vorschau

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Warum muss ich NodeJS für die Vorschau der überprüfbaren Anmeldeinformationen verwenden? Gibt es Pläne für andere Programmiersprachen? 

Wir haben uns für NodeJS entschieden, da es sich um eine sehr beliebte Plattform für Anwendungsentwickler handelt. Wir werden eine REST-API veröffentlichen, mit der Entwickler Anmeldeinformationen ausgeben und überprüfen können. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Wird ein Teil des Codes in der Open Source-Vorschau verwendet?

Ja! Die folgenden Repositorys sind die Open-Source-Komponenten unserer Dienste.

1. [SideTree, auf GitHub](https://github.com/decentralized-identity/sidetree)
2. Die [VC-SDK für Node, auf GitHub](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. Eine [Android-SDK zum Erstellen von dezentralisierten Identity Wallets, auf GitHub](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. Eine [iOS-SDK zum Erstellen von dezentralisierten Identity Wallets, auf GitHub](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Welche Lizenzbedingungen gelten?

Eine Azure AD P2-Lizenz ist erforderlich, um die Vorschau der überprüfbaren Anmeldeinformationen zu verwenden. Diese Anforderung ist temporär, da wir die nutzungsbasierte Abrechnung dieses Dienstes planen. 


## <a name="next-steps"></a>Nächste Schritte

- [Vorgehensweise beim Anpassen Ihrer überprüfbaren Anmeldeinformationen für Azure Active Directory](credential-design.md)
