---
title: MFA oder 2FA und Privileged Identity Management – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Azure AD Privileged Identity Management (PIM) die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) überprüft.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e263de51c234c0572584911b3bd8f7d4eb7c487c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667210"
---
# <a name="multifactor-authentication-and-privileged-identity-management"></a>Mehrstufige Authentifizierung und Privileged Identity Management

Es wird empfohlen, eine mehr- oder zweistufige Authentifizierung (MFA oder 2FA) für alle Administratoren als erforderlich festzulegen. Eine mehrstufige Authentifizierung reduziert das Risiko eines Angriffs mithilfe eines kompromittierten Kennworts.

Sie können festlegen, dass Benutzer beim Anmelden eine mehrstufige Authentifizierung durchlaufen müssen. Sie können auch festlegen, dass Benutzer eine mehrstufige Authentifizierung durchlaufen müssen, wenn sie eine Rolle in Azure Active Directory Privileged Identity Management (PIM) aktivieren. Auf diese Weise werden Benutzer von PIM zur mehrstufigen Authentifizierung aufgefordert, auch wenn sie den Prozess bei der Anmeldung nicht durchlaufen haben.

> [!IMPORTANT]
> Zurzeit funktioniert Azure AD Multi-Factor Authentication nur mit Geschäfts-, Schul- oder Unikonten, aber nicht mit persönlichen Microsoft-Konten (normalerweise ein persönliches Konto, das zum Anmelden bei Microsoft-Diensten wie Skype, Xbox oder Outlook.com verwendet wird). Aus diesem Grund können Benutzer mit persönlichen Konten keine berechtigten Administratoren sein, da zum Aktivieren der Rollen die mehrstufige Authentifizierung verwendet werden muss. Wenn Benutzer weiterhin Workloads mit einem Microsoft-Konto verwalten müssen, stufen Sie sie vorerst zu permanenten Administratoren hoch.

## <a name="how-pim-validates-mfa"></a>Überprüfen der MFA durch PIM

Beim Aktivieren einer Rolle stehen zwei Optionen zum Überprüfen der mehrstufigen Authentifizierung zur Verfügung.

Die einfachste Methode besteht darin, sich bei Benutzern, die eine privilegierte Rolle aktivieren, auf Azure AD Multi-Factor Authentication zu verlassen. Überprüfen Sie dazu ggf. zunächst, ob diese Benutzer lizenziert sind und sich für Azure AD Multi-Factor Authentication registriert haben. Weitere Informationen zum Bereitstellen von Azure AD Multi-Factor Authentication finden Sie unter [Erste Schritte mit Azure AD Multi-Factor Authentication in der Cloud](../authentication/howto-mfa-getstarted.md). Es ist zwar nicht erforderlich, wird aber empfohlen, Azure AD so zu konfigurieren, dass die mehrstufige Authentifizierung für diese Benutzer bei der Anmeldung erzwungen wird. Der Grund hierfür ist, dass Überprüfungen der mehrstufigen Authentifizierung von Privileged Identity Management selbst durchgeführt werden.

Wenn sich Benutzer lokal authentifizieren, können Sie alternativ dazu auch festlegen, dass der Identitätsanbieter für die mehrstufige Authentifizierung verantwortlich ist. Wenn Sie beispielsweise AD-Verbunddienste so konfiguriert haben, dass vor dem Zugriff auf Azure AD eine smartcardbasierte Authentifizierung erforderlich ist, lesen Sie die Anweisungen zum Konfigurieren von AD FS für das Senden von Ansprüchen an Azure AD unter [Schützen von Cloudressourcen mit Azure AD Multi-Factor Authentication und AD FS](../authentication/howto-mfa-adfs.md). Wenn ein Benutzer versucht, eine Rolle zu aktivieren, akzeptiert Privileged Identity Management, dass die mehrstufige Authentifizierung für den Benutzer bereits überprüft wurde, sobald die entsprechenden Ansprüche empfangen werden.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
