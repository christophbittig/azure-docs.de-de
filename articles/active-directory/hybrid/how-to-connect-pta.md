---
title: 'Azure AD Connect: Passthrough-Authentifizierung | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie die Passthrough-Authentifizierung mit Azure Active Directory (Azure AD) funktioniert und wie sie Azure AD-Anmeldungen durch die Überprüfung von Benutzerkennwörtern anhand des lokalen Active Directory ermöglicht.
services: active-directory
keywords: Was ist die Pass-Through-Authentifizierung mit Azure AD Connect?, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0253debd0b667838dc0cb4cd7aec41a29cf9ce81
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059016"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Benutzeranmeldung mit der Azure Active Directory-Passthrough-Authentifizierung

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Was ist die Azure Active Directory-Passthrough-Authentifizierung?

Mit der Azure AD-Passthrough-Authentifizierung (Azure Active Directory) können sich Benutzer mit denselben Kennwörtern sowohl an lokalen als auch an cloudbasierten Anwendungen anmelden. Diese Funktion stellt eine benutzerfreundlichere Oberfläche für Ihre Benutzer bereit, weil ein Kennwort wegfällt, und reduziert die Kosten des IT-Helpdesks, da Benutzer seltener vergessen, wie sie sich anmelden. Wenn Benutzer sich mithilfe von Azure AD anmelden, überprüft dieses Feature die Benutzerkennwörter direkt anhand Ihres lokalen Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Diese Funktion ist eine Alternative zum [Kennworthashsynchronisierung von Azure AD](how-to-connect-password-hash-synchronization.md), die Organisationen auch eine Möglichkeit zur Cloudauthentifizierung bereitstellt. Allerdings können bestimmte Organisationen, die ihre lokale Active Directory-Sicherheits- und -Kennwortrichtlinien erzwingen möchten, stattdessen die Passthrough-Authentifizierung verwenden. Einen Vergleich der verschiedenen Azure AD-Anmeldemethoden und Informationen, wie Sie die richtige Anmeldemethode für Ihre Organisation wählen, finden Sie in [diesem Leitfaden](./choose-ad-authn.md).

![Azure AD-Passthrough-Authentifizierung](./media/how-to-connect-pta/pta1.png)

Sie können die Passthrough-Authentifizierung mit dem Feature zum [nahtlosen einmaligen Anmelden](how-to-connect-sso.md) (Single Sign-On, SSO) kombinieren. Auf diese Weise müssen Ihre Benutzer, wenn sie über ihren Unternehmenscomputer innerhalb des Unternehmensnetzwerks auf Anwendungen zugreifen, nicht einmal ihre Kennwörter eingeben.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Wichtige Vorteile der Azure AD-Passthrough-Authentifizierung

- *Große Benutzerfreundlichkeit*
  - Benutzer verwenden für die Anmeldung bei lokalen und cloudbasierten Anwendungen das gleiche Kennwort.
  - Benutzer verbringen weniger Zeit mit dem gemeinsamen Lösen von Kennwortproblemen mit dem IT-Helpdesk
  - Benutzer können die [Self-Service-Kennwortverwaltung](../authentication/concept-sspr-howitworks.md) selbst in der Cloud durchführen.
- *Einfache Bereitstellung und Verwaltung*
  - Bereitstellungen oder Netzwerkkonfigurationen müssen nicht mehr komplex und lokal sein.
  - Es muss nur ein einfacher Agent lokal installiert werden.
  - Es gibt keinen zusätzlichen Verwaltungsaufwand. Der Agent empfängt Verbesserungen und Fehlerbehebungen automatisch.
- *Sicher*
  - Lokale Kennwörter werden niemals in irgendeiner Form in der Cloud gespeichert.
  - Ihre Benutzerkonten werden durch die nahtlose Kompatibilität mit [Azure AD-Richtlinien für bedingten Zugriff](../conditional-access/overview.md), einschließlich der Multi-Factor Authentication (MFA), durch das [Blockieren der Legacyauthentifizierung](../conditional-access/concept-conditional-access-conditions.md) und durch das [Herausfiltern von Brute-Force-Kennwortangriffen](../authentication/howto-password-smart-lockout.md) geschützt.
  - Der Agent stellt innerhalb des Netzwerks nur ausgehende Verbindungen her. Daher muss der Agent nicht in einem Umkreisnetzwerk (auch als DMZ bezeichnet) installiert werden.
  - Die Kommunikation zwischen einem Agent und Azure AD wird per zertifikatbasierter Authentifizierung geschützt. Diese Zertifikate werden alle paar Monate automatisch von Azure AD verlängert.
- *Hoch verfügbar*
  - Zusätzliche Agents können auf mehrere lokalen Servern installiert werden, um Hochverfügbarkeit von Anmeldeanforderungen bereitzustellen.

## <a name="feature-highlights"></a>Wichtige Features

- Benutzeranmeldungen in allen browserbasierten Webanwendungen und Microsoft Office-Clientanwendungen werden unterstützt, die die [moderne Authentifizierung](https://aka.ms/modernauthga) verwenden.
- Bei den Benutzernamen für die Anmeldung kann es sich entweder um den lokalen Standardbenutzernamen (`userPrincipalName`) oder um ein anderes (als `Alternate ID` bezeichnetes) Attribut handeln, das in Azure AD Connect konfiguriert ist.
- Das Feature funktioniert nahtlos mit Features für den [bedingten Zugriff](../conditional-access/overview.md) wie die Multi-Factor Authentication (MFA), mit der Sie Ihre Benutzer schützen können.
- Es ist in eine cloudbasierte [Self-Service-Kennwortverwaltung](../authentication/concept-sspr-howitworks.md) integriert, einschließlich Rückschreiben von Kennwörtern auf lokalem Active Directory und Kennwortschutz, indem häufig verwendete Kennwörter gesperrt werden.
- Umgebungen mit mehreren Gesamtstrukturen werden unterstützt, wenn Gesamtstruktur-Vertrauensstellungen zwischen Ihren AD-Gesamtstrukturen bestehen und das Namensuffixrouting ordnungsgemäß konfiguriert ist.
- Es handelt sich um ein kostenloses Feature, sodass Sie für dessen Verwendung keine kostenpflichtigen Editionen von Azure AD benötigen.
- Dies kann über [Azure AD Connect](whatis-hybrid-identity.md) aktiviert werden.
- Dazu wird ein einfacher lokaler Agent verwendet, der auf Kennwortüberprüfungsanforderungen lauscht und darauf reagiert.
- Die Installation von mehreren Agents stellt Hochverfügbarkeit von Anmeldeanforderungen bereit.
- So werden Ihre lokalen Konten gegen Brute-Force-Kennwortangriffe in der Cloud [geschützt](../authentication/howto-password-smart-lockout.md).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart](how-to-connect-pta-quick-start.md): Einrichten und Ausführen der Passthrough-Authentifizierung mit Azure AD.
- [Migrieren von AD FS zur Passthrough-Authentifizierung](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true): Ein detaillierter Leitfaden zur Migration von AD FS (oder anderen Verbundtechnologien) zur Passthrough-Authentifizierung
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Konfigurieren der Smart Lockout-Funktion für Ihren Mandanten zum Schutz der Benutzerkonten.
- [Aktuelle Einschränkungen](how-to-connect-pta-current-limitations.md): Informationen zu den unterstützten und nicht unterstützten Szenarien
- [Technische Einzelheiten](how-to-connect-pta-how-it-works.md) – Funktionsweise dieses Features verstehen
- [Häufig gestellte Fragen:](how-to-connect-pta-faq.yml)  Antworten auf häufig gestellte Fragen
- [Problembehandlung](tshoot-connect-pass-through-authentication.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [Ausführliche Informationen zur Sicherheit](how-to-connect-pta-security-deep-dive.md): zusätzliche ausführliche technische Informationen zum Feature.
- [Nahtlose SSO mit Azure AD](how-to-connect-sso.md): Informationen zu dieser Ergänzungsfunktion
- [UserVoice:](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)  Verfassen neuer Feature-Anforderungen