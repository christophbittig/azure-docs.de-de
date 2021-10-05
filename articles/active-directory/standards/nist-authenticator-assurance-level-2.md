---
title: Realisieren von NIST AAL2 mit Azure Active Directory
description: Anleitung zum Realisieren von NIST AAL 2 (Authenticator Assurance Level 2) mit Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30287957ca9d44c27f0d46efb6ae358db7d6ab44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662265"
---
# <a name="achieve-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Realisieren von NIST Authenticator Assurance Level 2 mit Azure Active Directory

Das National Institute of Standards and Technology (NIST) entwickelt die technischen Anforderungen für US-Bundesbehörden, die Identitätslösungen implementieren. Die Erfüllung dieser Anforderungen ist auch für Organisationen erforderlich, die mit Bundesbehörden zusammenarbeiten. 

Bevor Sie versuchen, AAL2 (Authenticator Assurance Level 2) zu realisieren, sollten Sie die folgenden Ressourcen lesen:
* [NIST-Übersicht](nist-overview.md): Grundlegende Informationen zu den verschiedenen AAL-Stufen.
* [Authentifizierungsgrundlagen](nist-authentication-basics.md): Wichtige Begriffe und Authentifizierungstypen.
* [NIST-Authentifikatortypen](nist-authenticator-types.md): Grundlegende Informationen zu den einzelnen Authentifikatortypen.
* [NIST AALs](nist-about-authenticator-assurance-levels.md): Komponenten der AALs und ihre Zuordnung zu Azure Active Directory(Azure AD)-Authentifizierungsmethoden.

## <a name="permitted-authenticator-types"></a>Zulässige Authentifikatortypen

Die folgende Tabelle enthält Details zu den Authentifikatortypen, die für AAL2 zulässig sind:

| Azure AD-Authentifizierungsmethode| NIST-Authentifikatortyp | 
| - | - |
| **Empfohlene Methoden** |   | 
| Microsoft Authenticator-App für iOS (kennwortlos)<br>Windows Hello for Business mit Software-TPM (Trusted Platform Module) | Multi-Factor-Kryptografiesoftware |
| FIDO 2-Sicherheitsschlüssel<br>Microsoft Authenticator-App für Android (kennwortlos)<br>Windows Hello for Business mit Hardware-TPM<br>Smartcard (Active Directory-Verbunddienste) | Multi-Factor-Kryptografiehardware |
| **Weitere Methoden** |  |
| Kennwort + Telefon (SMS) | Gespeichertes Geheimnis + Out-of-Band |
| Kennwort + Microsoft Authenticator-App (OTP)<br>Kennwort + SF OTP | Gespeichertes Geheimnis +  ‎Single-Factor-Einmalkennwort |
| Kennwort + in Azure AD eingebettetes Gerät mit Software-TPM <br>Kennwort + konformes mobiles Gerät<br>Kennwort + in Azure AD Hybrid eingebettetes Gerät mit Software-TPM <br>Kennwort + Microsoft Authenticator-App (Benachrichtigung) | Gespeichertes Geheimnis + ‎Single-Factor-Kryptografiesoftware |
| Kennwort + in Azure AD eingebettetes Gerät mit Hardware-TPM <br>Kennwort + in Azure AD Hybrid eingebettetes Gerät mit Hardware-TPM | Gespeichertes Geheimnis + Single-Factor-Kryptografiehardware |

> [!NOTE]
> Wenn Sie in einer Richtlinie für bedingten Zugriff fordern, dass ein Gerät entweder als konform oder hybrid Azure AD verbunden ist, fungiert Authenticator als Identitätswechsel-Widerstand bei der Verifizierer.

### <a name="our-recommendations"></a>Empfehlungen

Um AAL2 zu realisieren, verwenden Sie Multi-Factor-Kryptografiehardware- oder softwareauthentifikatoren. Kennwortlose Authentifizierung beseitigt die größte Angriffsfläche (das Kennwort) und bietet Benutzern eine optimierte Methode zur Authentifizierung. 

Einen detaillierten Leitfaden zur Auswahl einer kennwortlosen Authentifizierungsmethode finden Sie unter [Planen einer Bereitstellung mit kennwortloser Authentifizierung in Azure Active Directory](../authentication/howto-authentication-passwordless-deployment.md).

Weitere Informationen zum Implementieren von Windows Hello for Business finden Sie im [Windows Hello for Business-Bereitstellungshandbuch.](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)

## <a name="fips-140-validation"></a>FIPS 140-Validierung

In den folgenden Abschnitten wird erläutert, wie sich FIPS 140-Validierung realisieren lässt.

### <a name="verifier-requirements"></a>Anforderungen an Überprüfer

Azure AD verwendet für alle seine kryptografischen Authentifizierungsvorgänge das nach Windows FIPS 140 Level 1 gesamtvalidierte kryptografische Modul. Es handelt sich daher um einen FIPS 140-konformen Überprüfer, wie von Regierungsbehörden gefordert.

### <a name="authenticator-requirements"></a>Anforderungen an Authentifikatoren

Die kryptografischen Authentifikatoren von Regierungsbehörden müssen nach FIPS 140 Level 1 gesamtvalidiert sein. Diese Anforderung gilt nicht für Institutionen, die keine Regierungsbehörden sind. Die folgenden Azure AD-Authentifikatoren erfüllen die Anforderung, wenn sie unter [Windows in einem FIPS 140-genehmigten Betriebsmodus](/windows/security/threat-protection/fips-140-validation) ausgeführt werden:

* Kennwort

* In Azure AD eingebettetes Gerät mit Software- oder Hardware-TPM

* In Azure AD Hybrid eingebettetes Gerät mit Software- oder Hardware-TPM

* Windows Hello for Business mit Software- oder Hardware-TPM

* Smartcard (Active Directory-Verbunddienste) 

Die Microsoft Authenticator-App verwendet zwar in allen Modi (Benachrichtigung, OTP und kennwortlos) FIPS 140-genehmigte Kryptografie, ist jedoch nicht nach FIPS 140 Level 1 validiert.

FIDO2-Sicherheitsschlüsselanbieter befinden sich in verschiedenen Phasen der FIPS-Zertifizierung, und in einigen Fällen wurde die Validierung abgeschlossen. Es wird empfohlen, die Liste [der unterstützten FIDO2-Schlüsselanbieter](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers) zu überprüfen und sich beim jeweiligen Anbieter nach dem aktuellen FIPS-Überprüfungsstatus zu erkundigen.


## <a name="reauthentication"></a>Erneute Authentifizierung 

Auf AAL2-Ebene erfordert NIST alle 12 Stunden unabhängig von der Benutzeraktivität eine erneute Authentifizierung. Eine erneute Authentifizierung ist auch jeweils nach mindestens 30 Minuten der Inaktivität erforderlich. Sie müssen etwas vorweisen, das Ihnen bekannt ist oder sich auf Ihre Person bezieht, da es sich bei dem Sitzungsgeheimnis um etwas handelt, das Sie haben.

Um die Anforderung einer erneuten Authentifizierung unabhängig von der Benutzeraktivität zu erfüllen, empfiehlt Microsoft, die [Anmeldehäufigkeit für Benutzer](../conditional-access/howto-conditional-access-session-lifetime.md) auf 12 Stunden zu konfigurieren. 

NIST ermöglicht auch die Verwendung kompensierender Kontrollen, um die Anwesenheit des Abonnenten zu bestätigen:

* Sie können das Sitzungsinaktivitätstimeout auf 30 Minuten festlegen, indem Sie das Gerät über Microsoft System Center Configuration Manager, Gruppenrichtlinienobjekte (GPOs) oder Intune auf Betriebssystemebene sperren. Sie müssen auch lokale Authentifizierung anfordern, damit der Abonnent es entsperren kann.

* Ein aktivitätsunabhängiges Timeout kann erreicht werden, indem eine geplante Aufgabe ausgeführt wird (mit Configuration Manager, GPO oder Intune), die den Computer unabhängig von der Aktivität nach 12 Stunden sperrt.

## <a name="man-in-the-middle-resistance"></a>Man-in-the-Middle-Widerstand 

Sämtliche Kommunikation zwischen dem Anforderer und Azure AD erfolgt über einen authentifizierten geschützten Kanal, um Widerstand gegen Man-in-the-Middle(MitM)-Angriffe bereitzustellen. Dies erfüllt die MitM-Widerstandsanforderungen für AAL1, AAL2 und AAL3.

## <a name="replay-resistance"></a>Replay-Widerstand

Alle Azure AD-Authentifizierungsmethoden nach AAL2 verwenden entweder Nonce oder Herausforderungen. Die Methoden sind widerstandsfähig gegen Wiederholungsangriffe, da der Verifizierer wiederholte Authentifizierungstransaktionen leicht erkennt. Solche Transaktionen enthalten nicht die entsprechenden Nonce- oder Zeitachsendaten.

## <a name="next-steps"></a>Nächste Schritte 

[NIST-Übersicht](nist-overview.md)

[Weitere Informationen zu AALs](nist-about-authenticator-assurance-levels.md)

[Authentifizierungsszenarien für Azure AD](nist-authentication-basics.md)

[NIST-Authentifikatortypen](nist-authenticator-types.md)

[Erzielen der NIST-Authentifikator-Sicherheitsstufe 1 mit Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Realisieren von NIST AAL2 mit Azure AD](nist-authenticator-assurance-level-2.md)

[Realisieren von NIST AAL3 mit Azure AD](nist-authenticator-assurance-level-3.md)
