---
title: Realisieren von NIST AAL3 mit Azure Active Directory
description: Dieser Artikel enthält Anleitungen zum Realisieren der NIST Authenticator Assurance Level 3 (AAL3) mit Azure Active Directory.
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
ms.openlocfilehash: d95bfadd23e1a8e0d2db70952c81667d24dc4230
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771022"
---
# <a name="achieve-nist-authenticator-assurance-level-3-by-using-azure-active-directory"></a>Realisieren von NIST Authenticator Assurance Level 3 mit Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie NIST AAL3 (National Institute of Standards and Technology Authenticator Assurance Level 3) realisieren. Sie sollten diese Ressourcen überprüfen, bevor Sie versuchen, AAL3 zu realisieren:
* [NIST-Übersicht](nist-overview.md): Grundlegende Informationen zu den verschiedenen AAL-Stufen.
* [Authentifizierungsgrundlagen](nist-authentication-basics.md): Wichtige Begriffe und Authentifizierungstypen.
* [NIST-Authentifikatortypen](nist-authenticator-types.md): Grundlegende Informationen zu den einzelnen Authentifikatortypen.
* [NIST AALs](nist-about-authenticator-assurance-levels.md): Komponenten der AALs und ihre Zuordnung zu Azure Active Directory-Authentifizierungsmethoden (Azure AD).

## <a name="permitted-authenticator-types"></a>Zulässige Authentifikatortypen
Microsoft bietet Authentifizierungsmethoden, mit denen Sie die erforderlichen NIST-Authentifikatortypen erfüllen können. Dieser Abschnitt enthält Empfehlungen von Microsoft.
 

| Azure AD-Authentifizierungsmethoden| NIST-Authentifikatortyp |
| - | -|
| **Empfohlene Methoden**|    |
| FIDO2-Sicherheitsschlüssel<br>oder<br> Smartcard (Active Directory-Verbunddienste [AD FS])<br>oder<br>Windows Hello for Business mit Hardware-TPM| Multi-Factor-Kryptografiehardware |
| **Weitere Methoden**|   |
| Kennwort<br> und<br>(In Azure AD Hybrid eingebunden mit Hardware-TPM <br>oder <br> In Azure AD eingebunden mit Hardware-TPM)| Gespeichertes Geheimnis<br>und<br> Single-Factor-Kryptografiehardware |
| Kennwort <br>und<br>Single-Factor-Einmalkennwort-Hardware (von einem OTP-Hersteller) <br>oder<br>In Azure AD Hybrid eingebunden mit Software TPM <br>oder <br> In Azure AD eingebunden mit Software TPM <br>oder<br> Kompatibles verwaltetes Gerät)| Gespeichertes Geheimnis <br>und<br>Single-Factor-Einmalkennwort-Hardware<br> und<br>Single-Factor-Kryptografiesoftware |

### <a name="our-recommendations"></a>Empfehlungen 

Es wird empfohlen, einen Multi-Factor-Kryptografiehardware-Authentifikator zu verwenden, um AAL3 zu erzielen. Die kennwortlose Authentifizierung beseitigt die größte Angriffsfläche – das Kennwort – und bietet Benutzern eine optimierte Authentifizierungsmethode. Für vollständig cloudbasierte Organisationen wird die Verwendung von FIDO2-Sicherheitsschlüsseln empfohlen.

Hinweis: Windows Hello for Business wurde nicht gemäß der erforderlichen FIPS 140-Sicherheitsstufe validiert. Daher müssen Kunden wie z. B. US-Bundesbehörden eine Risikobewertung und Evaluierung durchführen, bevor sie es als AAL3 akzeptieren. .

Einen detaillierten Leitfaden finden Sie unter [Planen einer Bereitstellung mit kennwortloser Authentifizierung in Azure Active Directory](../authentication/howto-authentication-passwordless-deployment.md).

Weitere Informationen zum Implementieren von Windows Hello for Business finden Sie im [Windows Hello for Business-Bereitstellungshandbuch.](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)

## <a name="fips-140-validation"></a>FIPS 140-Validierung

### <a name="verifier-requirements"></a>Anforderungen an Überprüfer

Azure AD verwendet das nach Windows FIPS 140 Level 1 gesamtvalidierte kryptografische   
‎Modul für alle mit der Authentifizierung verbundenen kryptografischen Vorgänge. Es handelt sich also um einen mit FIPS-140 konformen Verifizierer.

### <a name="authenticator-requirements"></a>Anforderungen an Authentifikatoren

Single-Factor- und Multi-Factor-Kryptografiehardware-Authentifikatoren weisen unterschiedliche Authentifikatoranforderungen auf. 

**Single-Factor-Kryptografiehardware**-Authentifikatoren müssen folgende Anforderungen erfüllen:

* Insgesamt: FIPS 140 Level 1 (oder höher).

* Physische Sicherheit: FIPS 140 Level 3 (oder höher).

In Azure AD und Azure AD Hybrid eingebundene Geräte erfüllen diese Anforderungen, wenn: 

* [Windows in einem FIPS 140-genehmigten Betriebsmodus](/windows/security/threat-protection/fips-140-validation) ausgeführt wird. 

* Die Ausführung auf einem Computer mit einem TPM erfolgt, das insgesamt FIPS 140 Level 1 (oder höher) erfüllt, und dessen physische Sicherheit FIPS 140 Level 3 entspricht. 

   * Suchen Sie auf der Steite [Cryptographic Module Validation Program](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search) nach „Trusted Platform Module“ und „TPM“, um kompatible TPMs zu finden.

Fragen Sie Ihren Mobilgeräteanbieter nach seiner FIPS 140-Compliance.

**Multi-Factor-Kryptografiehardware**-Authentifikatoren müssen folgende Anforderungen erfüllen: 

* Insgesamt: FIPS 140 Level 2 (oder höher).

* Physische Sicherheit: FIPS 140 Level 3 (oder höher).

FIDO2-Sicherheitsschlüssel, Smartcards und Windows Hello for Business können Ihnen helfen, diese Anforderungen zu erfüllen.

* FIDO2-Schlüsselanbieter befinden sich in verschiedenen Phasen der FIPS-Zertifizierung, und in einigen Fällen wurde die Validierung abgeschlossen. Es wird empfohlen, die Liste [der unterstützten FIDO2-Schlüsselanbieter](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers) zu überprüfen und sich beim jeweiligen Anbieter nach dem aktuellen FIPS-Überprüfungsstatus zu erkundigen.

* Smartcards sind eine bewährte Technologie. Mehrere Herstellerprodukte erfüllen FIPS-Anforderungen.

   * Weitere Informationen finden Sie auf der Seite [Cryptographic Module Validation Program](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

**Windows Hello for Business**

FIPS 140 setzt voraus, dass die gesamte kryptografische Grenze, einschließlich Software, Firmware und Hardware, im Evaluierungsumfang enthalten ist. Windows-Betriebssysteme sind offene Computingplattformen, die mit Tausenden von Kombinationen von Hardware gekoppelt werden können. Microsoft kann keine FIPS-Zertifizierungen für jede Kombination verwalten. Sie sollten die folgenden individuellen Zertifizierungen von Komponenten im Rahmen Ihrer Risikobewertung für die Verwendung von Windows Hello for Business als AAL3-Authentifikator auswerten:

* **Windows 10 und Windows Server** verwenden das [von der US-Regierung genehmigte Schutzprofil für universelle Betriebssysteme Version 4.2.1](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442) der National Information Assurance Partnership (NIAP). NIAP überwacht ein nationales Programm, mit dem handelsübliche IT-Produkte, sogenannte Commercial Off-The-Shelf- bzw. COTS-Produkte, auf Konformität mit den internationalen Common Criteria überprüft werden. 

* **Windows Cryptographic Library** [hat im NIST Cryptographic Module Validation Program (CMVP) insgesamt FIPS Level 1 erreicht](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544). Das CMVP, ein gemeinsames Projekt des NIST und des Canada Center for Cyber Security, überprüft kryptografische Module anhand von FIPS-Standards. 

* Wählen Sie ein **TPM (Trusted Platform Module)** aus, das insgesamt FIPS 140 Level 2 erfüllt und in Bezug auf physische Sicherheit FIPS 140 Level3 entspricht. Als Organisation müssen Sie sicherstellen, dass das von Ihnen verwendete Hardware-TPM den Anforderungen der AAL-Stufe entspricht, die Sie erzielen möchten.   
‎Um zu ermitteln, welche TPMs den aktuellen Standards entsprechen, navigieren Sie zur Seite [NIST Computer Security Resource Center Cryptographic Module Validation Program](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search). Geben Sie in das Feld **Module Name** den Namen **Trusted Platform Module** ein. Die sich ergebende Liste enthält Hardware-TPMS, die den aktuellen Standards entsprechen.

## <a name="reauthentication"></a>Erneute Authentifizierung 

Auf AAL3-Ebene erfordert NIST alle 12 Stunden unabhängig von der Benutzeraktivität erneute Authentifizierung. Erneute Authentifizierung ist auch jeweils nach mindestens 15 Minuten der Inaktivität erforderlich. Beide Faktoren müssen vorgewiesen werden.

Um die Anforderung einer erneuten Authentifizierung unabhängig von der Benutzeraktivität zu erfüllen, empfiehlt Microsoft, die [Anmeldehäufigkeit für Benutzer](../conditional-access/howto-conditional-access-session-lifetime.md) auf 12 Stunden zu konfigurieren. 

NIST ermöglicht auch die Verwendung kompensierender Kontrollen, um die Anwesenheit des Abonnenten zu bestätigen:

* Sie können ein Sitzungsinaktivitätstimeout von 15 Minuten festlegen, indem Sie das Gerät auf Betriebssystemebene sperren. Dies können Sie mithilfe von Microsoft Endpoint Configuration Manager, einem Gruppenrichtlinienobjekt (GPO) oder Intune erreichen. Sie müssen auch lokale Authentifizierung anfordern, damit der Abonnent es entsperren kann.

* Ein aktivitätsunabhängiges Timeout kann erreicht werden, indem eine geplante Aufgabe ausgeführt wird (mit Configuration Manager, GPO oder Intune), die den Computer unabhängig von der Aktivität nach 12 Stunden sperrt.

## <a name="man-in-the-middle-resistance"></a>Man-in-the-Middle-Widerstand 

Sämtliche Kommunikation zwischen dem Anforderer und Azure AD erfolgt über einen authentifizierten geschützten Kanal, um Widerstand gegen Man-in-the-Middle-Angriffe (MitM) bereitzustellen. Diese Konfiguration erfüllt die MitM-Widerstandsanforderungen für AAL1, AAL2 und AAL3.

## <a name="verifier-impersonation-resistance"></a>Widerstand gegen Identitätswechsel des Überprüfers

Alle Azure AD-Authentifizierungsmethoden, die AAL3 erfüllen, verwenden kryptografische Authentifikatoren, die die Authentifikatorausgabe an die jeweils zu authentifizierende Sitzung binden. Dazu verwenden sie einen privaten Schlüssel, der der Kontrolle des Anforderers unterliegt, für den der öffentliche Schlüssel dem Überprüfer bekannt ist. Diese Konfiguration erfüllt die Anforderungen an den Identitätswechsel von Überprüfern für AAL3.

## <a name="verifier-compromise-resistance"></a>Widerstand gegen Kompromittierung von Überprüfern

Alle Azure AD-Authentifizierungsmethoden, die AAL3 erfüllen, nutzen eine der folgenden Möglichkeiten:
- Sie verwenden einen kryptografischen Authentifikator, der erfordert, dass die Überprüfung einen öffentlichen Schlüssel speichert, der einem privaten Schlüssel entspricht, der vom Authentifikator gespeichert wird. 
- Sie speichern die erwartete Authentifikatorausgabe mit FIPS-140-validierten Hashalgorithmen. 

Weitere Informationen finden Sie unter [Überlegungen zur Sicherheit von Azure AD-Daten](https://aka.ms/AADDataWhitepaper).

## <a name="replay-resistance"></a>Replay-Widerstand

Alle Azure AD-Authentifizierungsmethoden, die AAL3 erfüllen, verwenden entweder Nonce oder Herausforderungen. Diese Methoden sind widerstandsfähig gegen Wiederholungsangriffe, da der Verifizierer wiederholte Authentifizierungstransaktionen leicht erkennen kann. Solche Transaktionen enthalten nicht die entsprechenden Nonce- oder Zeitachsendaten.

## <a name="authentication-intent"></a>Authentifizierungsabsicht

Die Authentifizierungsabsicht soll die Verwendung direkt verbundener physischer Authentifikatoren (z. B. Multi-Factor-Kryptografiegeräte) ohne das Wissen der betroffenen Person erschweren. Beispielsweise durch Schadsoftware auf dem Endpunkt.

NIST ermöglicht die Verwendung von kompensierenden Kontrollen zur Minderung des Schadsoftwarerisikos. Alle Intune-kompatiblen Geräte, auf denen Windows Defender-Systemüberwachung und Windows Defender ATP ausgeführt werden, erfüllen diese Entschärfungsanforderung.

## <a name="next-steps"></a>Nächste Schritte 

[NIST-Übersicht](nist-overview.md)

[Weitere Informationen zu AALs](nist-about-authenticator-assurance-levels.md)

[Authentifizierungsszenarien für Azure AD](nist-authentication-basics.md)

[NIST-Authentifikatortypen](nist-authenticator-types.md)

[Erreichen von NIST AAL1 mit Azure AD](nist-authenticator-assurance-level-1.md)

[Erreichen von NIST AAL2 mit Azure AD](nist-authenticator-assurance-level-2.md)
