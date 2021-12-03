---
title: Azure AD Multi-Factor Authentication – Versionen und Verbrauchstarife
description: Lernen Sie den Client für Azure AD Multi-Factor Authentication sowie die verschiedenen verfügbaren Methoden und Versionen kennen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dff3c04ee74d17aeb0b96d8d0e2348a9667f9729
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456307"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Features und Lizenzen für Azure AD Multi-Factor Authentication

Zum Schutz von Benutzerkonten in Ihrer Organisation sollten Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verwenden. Dieses Feature ist insbesondere für Konten wichtig, die privilegierten Zugriff auf Ressourcen haben. Grundlegende Funktionen für die mehrstufige Authentifizierung stehen für globale Administratoren von Microsoft 365 und Azure Active Directory (Azure AD) ohne zusätzliche Kosten zur Verfügung. Wenn Sie für Ihre Administratoren ein Upgrade der Funktionen durchführen oder die mehrstufige Authentifizierung auf die übrigen Benutzer ausweiten möchten, können Sie Azure AD Multi-Factor Authentication auf mehreren Wegen erwerben.

> [!IMPORTANT]
> In diesem Artikel werden die verschiedenen Möglichkeiten für die Lizenzierung und Verwendung von Azure AD Multi-Factor Authentication erläutert. Ausführliche Informationen zur Preisgestaltung und Abrechnung finden Sie auf der Seite [Azure AD – Preise](https://www.microsoft.com/en-us/security/business/identity-access-management/azure-ad-pricing).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Verfügbare Versionen von Azure AD Multi-Factor Authentication

Azure AD Multi-Factor Authentication kann entsprechend den Anforderungen Ihrer Organisation auf unterschiedliche Weise verwendet und lizenziert werden. Abhängig von der Lizenz für Azure AD, EMS oder Microsoft 365, die Sie zurzeit besitzen, sind Sie möglicherweise bereits berechtigt, Azure AD Multi-Factor Authentication zu verwenden. Beispielsweise können die ersten 50.000 monatlich aktiven Benutzer in Azure AD External Identities MFA und weitere Premium P1- oder P2-Features kostenlos nutzen. Weitere Informationen finden Sie unter [Externe Azure Active Directory-Identitäten – Preise](https://azure.microsoft.com/pricing/details/active-directory/external-identities/).

In der folgenden Tabelle werden die verschiedenen Möglichkeiten zum Erwerb von Azure AD Multi-Factor Authentication erläutert sowie jeweils einige Funktionen und Anwendungsfälle beschrieben.

| Benutzer von | Funktionen und Anwendungsfälle |
| --- | --- |
| [Microsoft 365 Business Premium](https://www.microsoft.com/microsoft-365/business) und [EMS](https://www.microsoft.com/security/business/enterprise-mobility-security) oder [Microsoft 365 E3 und E5](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans) | EMS E3, Microsoft 365 E3 und Microsoft 365 Business Premium enthalten Azure AD Premium P1. EMS E5 oder Microsoft 365 E5 umfasst Azure AD Premium P2. Sie können die in den folgenden Abschnitten beschriebenen Funktionen für den bedingten Zugriff auch für die Bereitstellung der mehrstufigen Authentifizierung für Benutzer verwenden. |
| [Azure AD Premium P1](../fundamentals/active-directory-get-started-premium.md) | Mit dem [bedingten Zugriff von Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) können Sie Benutzer bei bestimmten Szenarien oder Ereignissen zur mehrstufigen Authentifizierung auffordern und auf diese Weise Ihre geschäftlichen Anforderungen erfüllen. |
| [Azure AD Premium P2](../fundamentals/active-directory-get-started-premium.md) | Bietet die höchste Sicherheitsstufe und eine verbesserte Benutzerumgebung. Erweitert die Funktionen von Azure AD Premium P1 um den [risikobasierten bedingten Zugriff](../conditional-access/howto-conditional-access-policy-risk.md), der sich an Benutzermuster anpasst und Eingabeaufforderungen für die mehrstufige Authentifizierung minimiert. |
| [Alle Microsoft 365-Pläne](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans) | Azure AD Multi-Factor Authentication kann mithilfe der [Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) für alle Benutzer aktiviert werden. Die Verwaltung von Azure AD Multi-Factor Authentication erfolgt über das Microsoft 365-Portal. Führen Sie zugunsten einer optimierten Benutzerumgebung ein Upgrade auf Azure AD Premium P1 oder P2 durch, und verwenden Sie den bedingten Zugriff. Weitere Informationen finden Sie unter [Schützen von Microsoft 365-Ressourcen mit der mehrstufigen Authentifizierung](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication).  |
| [Kostenlose Office 365-Version](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans)<br>[Kostenlose Azure AD-Version](../verifiable-credentials/how-to-create-a-free-developer-account.md) | Sie können die Benutzer bei Bedarf mithilfe der [Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) zur Verwendung der mehrstufigen Authentifizierung auffordern. Sie haben keine genaue Kontrolle über aktivierte Benutzer oder Szenarien, aber dieser zusätzliche Sicherheitsschritt wird bereitgestellt.<br /> Auch wenn die Sicherheitsstandards nicht zum Aktivieren der mehrstufigen Authentifizierung für alle Benutzer verwendet werden, können Benutzer mit der Rolle *globaler Azure AD-Administrator* für die Verwendung der mehrstufigen Authentifizierung konfiguriert werden. Diese Funktion der kostenlosen Version stellt sicher, dass die kritischen Administratorkonten durch die mehrstufige Authentifizierung geschützt sind. |

## <a name="feature-comparison-based-on-licenses"></a>Featurevergleich basierend auf Lizenzen

In der folgenden Tabelle werden die Funktionen aufgeführt, die in den verschiedenen Versionen von Azure AD Multi-Factor Authentication zur Verfügung stehen. Planen Sie Ihre Anforderungen an eine sichere Benutzerauthentifizierung, und legen Sie dann fest, welcher Ansatz diese Anforderungen erfüllt. Beispiel: Obwohl Azure AD Free Azure AD Multi-Factor Authentication über Sicherheitsstandards bereitstellt, kann für Authentifizierungsaufforderungen nur die mobile Authentifikator-App und kein Telefonanruf bzw. keine SMS verwendet werden. Dieser Ansatz kann eine Einschränkung darstellen, wenn Sie nicht gewährleisten können, dass die Authentifikator-App auf dem persönlichen Gerät des Benutzers installiert ist. Ausführlichere Informationen finden Sie im Abschnitt [Azure AD Free-Tarif](#azure-ad-free-tier) weiter unten in diesem Thema. 

| Funktion | Azure AD Free – Sicherheitsstandards (für alle Benutzer aktiviert) | Azure AD Free – nur globale Administratoren | Office 365 | Azure AD Premium P1 oder P2 |
| --- |:---:|:---:|:---:|:---:|
| Schutz von Azure AD-Administratorkonten eines Mandanten mit MFA | ● | ● (gilt nur für *globale Azure AD-Administratorkonten*) | ● | ● |
| Mobile App als zweiter Faktor | ● | ● | ● | ● |
| Telefonanruf als zweiter Faktor | | ● | ● | ● |
| SMS als zweiter Faktor | | ● | ● | ● |
| Administrative Kontrolle über Überprüfungsmethoden | | ● | ● | ● |
| Betrugswarnung | | | | ● |
| MFA-Berichte | | | | ● |
| Benutzerdefinierte Begrüßungen für Telefonanrufe | | | | ● |
| Benutzerdefinierte Anrufer-ID für Telefonanrufe | | | | ● |
| Vertrauenswürdige IP-Adressen | | | | ● |
| Speichern der MFA für vertrauenswürdige Geräte | | ● | ● | ● |
| MFA für lokale Anwendungen | | | | ● |

## <a name="compare-multi-factor-authentication-policies"></a>Vergleichen von Richtlinien für die mehrstufige Authentifizierung

Unser empfohlener Ansatz zum Erzwingen von MFA ist das Verwenden des [bedingten Zugriffs](../conditional-access/overview.md). Prüfen Sie die folgende Tabelle, um zu ermitteln, welche Funktionen in Ihren Lizenzen enthalten sind.

| -Richtlinie | Standardwerte für die Sicherheit | Bedingter Zugriff | Benutzerbasierte MFA |
| --- |:---:|:---:|:---:|
| **Verwaltung** | 
| Standardgruppe von Sicherheitsregeln, um die Sicherheit Ihres Unternehmens zu gewährleisten | ● |  |  |
| Ein-/Ausschalten mit einem Klick | ● |  |  |
| In Office 365-Lizenzierung enthalten (siehe [Verfügbare Versionen von Azure AD Multi-Factor Authentication)](#available-versions-of-azure-ad-multi-factor-authentication) | ● |  | ● |
| Vorkonfigurierte Vorlagen im Microsoft 365 Admin Center-Assistenten | ● | ● |  |
| Konfigurationsflexibilität | | ● |  |
| **Funktion** | 
| Ausschließen von Benutzer*innen von der Richtlinie | | ● | ● |
| Authentifizieren per Telefonanruf oder SMS | | ● | ● |
| Authentifizieren durch Microsoft Authenticator und Softwaretoken | ● | ● | ● |
| Authentifizieren durch FIDO2- und Windows Hello for Business-Token sowie durch Hardwaretoken | | ● | ● |
| Blockieren von Legacyauthentifizierungsprotokollen | ● | ● | ● |
| Automatischer Schutz für neue Mitarbeiter | ● | ● | |
| Dynamische MFA-Trigger basierend auf Risikoereignissen | | ● |  |
| Richtlinien für die Authentifizierung und Autorisierung | | ● | |
| Konfigurierbarkeit basierend auf Standort und Gerätestatus | | ● | |
| Unterstützung für den Modus „Nur Bericht“ | | ● | |
| Möglichkeit zum vollständigen Blockieren von Benutzer*innen/Diensten | | ● | |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Erwerben und Aktivieren von Azure AD Multi-Factor Authentication

Registrieren Sie sich für einen berechtigten Azure AD-Tarif (oder erwerben Sie einen solchen Tarif), um Azure AD Multi-Factor Authentication verwenden zu können. Azure AD ist in vier Editionen erhältlich: Free, Office 365, Premium P1 und Premium P2.

Die kostenlose Version (Free) ist im Azure-Abonnement enthalten. Im [folgenden Abschnitt](#azure-ad-free-tier) finden Sie Informationen zur Verwendung der Sicherheitsstandards bzw. zum Schutz von Konten mit der Rolle *globaler Azure AD-Administrator*.

Die Azure AD Premium-Editionen sind über Ihren Microsoft-Vertreter, das [Open Volume License-Programm](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) und das [Cloud Solution Providers-Programm](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409) erhältlich. Azure- und Microsoft 365-Abonnenten können Azure Active Directory Premium P1 und P2 auch online erwerben. Für einen Kauf müssen Sie sich [anmelden](https://portal.office.com/Commerce/Catalog.aspx).

Nachdem Sie den erforderlichen Azure AD-Tarif erworben haben, müssen Sie [Azure AD Multi-Factor Authentication planen und bereitstellen](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Tarif „Azure AD Free“

Alle Benutzer in einem Mandanten der Edition „Azure AD Free“ können mithilfe der Sicherheitsstandards Azure Multi-Factor Authentication nutzen. Bei Verwendung der Sicherheitsstandards der Edition „Azure AD Free“ kann nur die mobile Authentifikator-App für Azure AD Multi-Factor Authentication verwendet werden.

* [Weitere Informationen zu Azure AD-Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md)
* [Aktivieren von Sicherheitsstandards für Benutzer von Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Wenn Sie Azure AD Multi-Factor Authentication nicht für alle Benutzer aktivieren möchten, können Sie stattdessen Benutzerkonten nur mit der Rolle *globaler Azure AD-Administrator* schützen. Bei diesem Ansatz werden zusätzliche Authentifizierungsaufforderungen für kritische Administratorkonten bereitgestellt. Sie können Azure AD Multi-Factor Authentication je nach verwendetem Kontotyp mit einer der folgenden Methoden aktivieren:

* Wenn Sie ein Microsoft-Konto verwenden, können Sie sich [für die mehrstufige Authentifizierung registrieren](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Falls Sie kein Microsoft-Konto verwenden, [aktivieren Sie die mehrstufige Authentifizierung für einen Benutzer oder eine Gruppe in Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Kosten finden Sie unter [Preise für Azure Active Directory](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).
* [Was ist bedingter Zugriff?](../conditional-access/overview.md)
* MFA kann auch [auf Benutzerbasis aktiviert werden](howto-mfa-userstates.md).

