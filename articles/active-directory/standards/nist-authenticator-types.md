---
title: NIST-Authentifikatortypen und angeglichene Azure Active Directory-Methoden
description: Erläuterungen zur Angleichung von Azure Active Directory Authentifizierungsmethoden an NIST-Authentifikatortypen.
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
ms.openlocfilehash: c3ee6cc0c913533b83be1eb389e7d7402875e4dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012414"
---
# <a name="nist-authenticator-types-and-aligned-azure-active-directory-methods"></a>NIST-Authentifikatortypen und angeglichene Azure Active Directory-Methoden

Der Authentifizierungsprozess beginnt, wenn ein Anforderer seine Kontrolle über einen von mehreren Authentifikatoren bestätigt, die einem Abonnenten zugeordnet sind. Bei dem Abonnenten kann es sich um eine Person oder eine andere Entität handeln.

| Der NIST-Authentifikatortyp (National Institute of Standards and Technology)| Azure Active Directory-Authentifizierungsmethoden (Azure AD) |
| - | - |
|  Gespeichertes Geheimnis <br> (etwas, das Sie wissen)|  Kennwort (Cloudkonten)  <br>Kennwort (Verbund)<br> Kennwort (Kennwort-Hashsynchronisierung)<br>Kennwort (Pass-Through-Authentifizierung) |
|Geheimnis nachschlagen <br> (etwas, das Sie haben)| Keine. Bei einem Lookupgeheimnis handelt es sich definitionsgemäß um Daten, die nicht in einem System gespeichert sind. |
|Out-of-band <br>(etwas, das Sie haben)| Telefon (SMS) – nicht empfohlen |
| Single-Factor-Einmalkennwort <br>‎(etwas, das Sie haben)| Microsoft Authenticator-App (einmaliges Kennwort)  <br>Single-Factor-Einmalkennwort ‎(über OTP-Hersteller)<sup data-htmlnode="">1</sup> | 
| Multi-Factor-Einmalkennwort<br>(etwas, das Sie haben + etwas, das Sie wissen oder etwas, das Sie sind)| Multi-Factor-Einmalkennwort ‎(über OTP-Hersteller) <sup data-htmlnode="">1</sup>| 
|Single-Factor-Kryptografiesoftware<br>(etwas, das Sie haben)|Kompatibles mobiles Gerät <br> Microsoft Authenticator-App (Benachrichtigung) <br> In Azure AD Hybrid eingebunden<sup data-htmlnode="">2</sup> mit Software-TPM<br> In Azure AD eingebunden<sup data-htmlnode="">2</sup> mit Software-TPM |
| Multi-Factor-Kryptografiehardware <br>(etwas, das Sie haben) | In Azure AD eingebunden<sup data-htmlnode="">2</sup> mit Hardware-TPM <br> In Azure AD Hybrid eingebunden<sup data-htmlnode="">2</sup> mit Hardware-TPM|
|Multi-Factor-Kryptografiesoftware<br>(etwas, das Sie haben + etwas, das Sie wissen oder etwas, das Sie sind) | Microsoft Authenticator-App für iOS (ohne Passwort)<br> Windows Hello for Business mit Software-TPM |
|Multi-Factor-Kryptografiehardware <br>(etwas, das Sie haben + etwas, das Sie wissen oder etwas, das Sie sind) |Microsoft Authenticator-App für Android (kennwortlos)<br> Windows Hello for Business mit Hardware-TPM<br> Smartcard (Verbundidentitätsanbieter) <br> FIDO 2 Sicherheitsschlüssel |


<sup data-htmlnode="">1</sup> OATH-TOTP SHA-1-Token der 30-Sekunden- oder 60-Sekunden-Variante.

<sup data-htmlnode="">2</sup> Weitere Informationen zu den Einbindungszuständen von Geräten finden Sie in der [Dokumentation zur Azure AD Geräteidentität](../devices/index.yml). 

## <a name="why-sms-isnt-recommended"></a>Warum SMS nicht empfohlen wird 

SMS-Textnachrichten erfüllen zwar den NIST-Standard, werden von NIST aber nicht empfohlen. Die Risiken von Gerätetausch, SIM-Wechseln, Nummernportierung und anderem Verhalten können Probleme verursachen. Wenn diese Aktionen böswillig ausgeführt werden, können sie zu unliebsamen Erfahrungen führen. SMS-Textnachrichten werden zwar nicht empfohlen, sind aber besser als die alleinige Verwendung eines Kennworts, weil sie mehr Aufwand für Hacker erfordern. 

## <a name="next-steps"></a>Nächste Schritte 

[NIST-Übersicht](nist-overview.md)

[Weitere Informationen zu AALs](nist-about-authenticator-assurance-levels.md)

[Authentifizierungsszenarien für Azure AD](nist-authentication-basics.md)

[NIST-Authentifikatortypen](nist-authenticator-types.md)

[Erzielen der NIST-Authentifikator-Sicherheitsstufe 1 mit Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Realisieren von NIST AAL2 mit Azure AD](nist-authenticator-assurance-level-2.md)

[Realisieren von NIST AAL3 mit Azure AD](nist-authenticator-assurance-level-3.md)
