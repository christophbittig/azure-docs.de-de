---
title: Unterstützte Azure AD-Features
description: Hier erfahren Sie mehr über Azure AD-Features, die in Azure AD B2C weiterhin unterstützt werden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 99f6d58af1a8508b6e3582b4ec0139081a259046
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042995"
---
# <a name="supported-azure-ad-features"></a>Unterstützte Azure AD-Features

Ein Azure AD B2C-Mandant unterscheidet sich von einem Azure Active Directory-Mandanten, wie Sie ihn möglicherweise bereits verwenden, basiert aber darauf. Die folgenden Azure AD-Features können in Ihrem Azure AD B2C-Mandanten verwendet werden.

|Funktion  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Gruppen](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Gruppen können zum Verwalten von Administrator- und Benutzerkonten verwendet werden.| Gruppen können zum Verwalten von Administratorkonten verwendet werden. [Consumerkonten](user-overview.md#consumer-user) dürfen keiner Gruppe angehören. |
| [Einladen von External Identities-Gästen](../active-directory//external-identities/add-users-administrator.md)| Sie können Gastbenutzer einladen und External Identities-Features wie Verbinden und Anmelden mit Facebook- und Google-Konten konfigurieren. | Sie können nur ein Microsoft-Konto oder einen Azure AD Benutzer als Gast zu Ihrem Azure AD-Mandanten einladen, um auf Anwendungen zuzugreifen oder Mandanten zu verwalten. Für [Consumer-Konten](user-overview.md#consumer-user) verwenden Sie Azure AD B2C Benutzerflows und benutzerdefinierte Richtlinien, um Benutzer zu verwalten und sich bei externen Identitätsanbietern wie Google oder Facebook zu registrieren oder anzumelden. |
| [Rollen und Administratoren](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Wird für Administrator- und Benutzerkonten vollständig unterstützt. | Rollen werden für [Consumer-Konten](user-overview.md#consumer-user)nicht unterstützt. Consumer-Konten haben keinen Zugriff auf Azure-Ressourcen.|
| [Benutzerdefinierte Domänennamen](../active-directory/fundamentals/add-custom-domain.md) |  Sie können Azure AD benutzerdefinierte Domänen nur für Administratorkonten verwenden. | [Consumer-Konten](user-overview.md#consumer-user) können sich mit einem Benutzernamen, einer Telefonnummer oder einer beliebigen E-Mail-Adresse anmelden. Sie können [benutzerdefinierte Domänen](custom-domain.md) in Ihren Umleitungs-URLs verwenden.|
| [Bedingter Zugriff](../active-directory/conditional-access/overview.md) | Wird für Administrator- und Benutzerkonten vollständig unterstützt. | Eine Teilmenge der Azure AD-Features für den bedingten Zugriff wird für [Consumerkonten](user-overview.md#consumer-user) unterstützt. Erfahren Sie, wie Sie den [bedingten Zugriff](conditional-access-user-flow.md) in Azure AD B2C konfigurieren.|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Vollständig unterstützt für Azure AD Premium P1-Features. Beispiele: [Kennwortschutz](../active-directory/authentication/concept-password-ban-bad.md), [Hybrididentitäten](../active-directory/hybrid/whatis-hybrid-identity.md), [Bedingter Zugriff](../active-directory/roles/permissions-reference.md#), [Dynamische Gruppen](../active-directory/enterprise-users/groups-create-rule.md) usw. | Eine Teilmenge der Azure AD-Features für den bedingten Zugriff wird für [Consumerkonten](user-overview.md#consumer-user) unterstützt. Erfahren Sie, wie Sie den [bedingten Zugriff](conditional-access-user-flow.md) in Azure AD B2C konfigurieren.|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Vollständig unterstützt für Azure AD Premium P2-Features. Beispiele: [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) und [Identity Governance](../active-directory/governance/identity-governance-overview.md).  | Eine Teilmenge der Azure AD Identity Protection-Features wird für [Consumerkonten](user-overview.md#consumer-user) unterstützt. Erfahren Sie, wie Sie [Risiken mit Identity Protection untersuchen](identity-protection-investigate-risk.md) und den [bedingten Zugriff](conditional-access-user-flow.md) in Azure AD B2C konfigurieren. |

> [!NOTE]
> **Andere Azure-Ressourcen in Ihrem Mandanten:** <br>In einem Azure AD B2C-Mandanten können Sie keine anderen Azure-Ressourcen wie virtuelle Computer, Azure-Web-Apps oder Azure-Funktionen bereitstellen. Sie müssen diese Ressourcen in Ihrem Azure AD erstellen.