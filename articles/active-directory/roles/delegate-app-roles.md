---
title: Delegieren von Administratorberechtigungen für die Anwendungsverwaltung – Azure AD | Microsoft-Dokumentation
description: Erteilen von Berechtigungen für die Anwendungszugriffsverwaltung in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd749289b9a389b495481517a56b2652fb2026f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444532"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegieren von App-Registrierungsberechtigungen in Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie über Berechtigungen, die von benutzerdefinierten Rollen in Azure Active Directory (Azure AD) gewährt werden, Ihre Anforderungen an die Anwendungsverwaltung erfüllen können. In Azure AD können Sie die Anwendungserstellung und Verwaltungsberechtigungen wie folgt delegieren:

- [Einschränken, welche Benutzer Anwendungen erstellen können](#restrict-who-can-create-applications), und Verwalten der erstellten Anwendungen. Standardmäßig können in Azure AD alle Benutzer Anwendungen registrieren und alle Aspekte der Anwendungen verwalten, die sie erstellen. Dies kann so eingeschränkt werden, dass diese Berechtigung nur für ausgewählte Personen gilt.
- [Zuweisen eines oder mehrerer Besitzer zu einer Anwendung](#assign-application-owners). Dies ist eine einfache Möglichkeit, einer Person die Berechtigung zu erteilen, alle Aspekte der Azure AD-Konfiguration für eine bestimmte Anwendung zu verwalten.
- [Zuweisen einer integrierten Administratorrolle](#assign-built-in-application-admin-roles), mit der Zugriff zum Verwalten der Konfiguration in Azure AD für alle Anwendungen gewährt wird. Dies ist die empfohlene Vorgehensweise, um IT-Experten Zugriff zum Verwalten von umfassenden Berechtigungen für die Anwendungskonfiguration zu gewähren, ohne Zugriff auf die Verwaltung anderer Bereiche von Azure AD zu gewähren, die nicht mit der Anwendungskonfiguration zusammenhängen.
- [Erstellen einer benutzerdefinierten Rolle](#create-and-assign-a-custom-role-preview), die sehr spezifische Berechtigungen definiert, und Zuweisen dieser Rolle zu einem Benutzer im Bereich einer einzelnen Anwendung als eingeschränkter Besitzer oder im Verzeichnisbereich (alle Anwendungen) als eingeschränkter Administrator.

Es ist aus zwei Gründen wichtig, den Zugriff mithilfe einer der oben genannten Methoden zu gewähren. Erstens wird durch die Delegierung der Möglichkeit zum Ausführen administrativer Aufgaben der Aufwand für globale Administratoren reduziert. Zweitens verbessert die Verwendung eingeschränkter Berechtigungen den Sicherheitsstatus und reduziert das Potenzial für nicht autorisierten Zugriff. Richtlinien zur Planung der Rollensicherheit finden Sie unter [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](security-planning.md).

## <a name="restrict-who-can-create-applications"></a>Einschränken, welche Benutzer Anwendungen erstellen können

Standardmäßig können in Azure AD alle Benutzer Anwendungen registrieren und alle Aspekte der Anwendungen verwalten, die sie erstellen. Alle Benutzer haben außerdem die Möglichkeit einzuwilligen, dass Apps in ihrem Namen auf Unternehmensdaten zugreifen. Sie können diese Berechtigungen selektiv erteilen, indem Sie die globalen Optionen auf „Nein“ festlegen und die ausgewählten Benutzer der Rolle „Anwendungsentwickler“ hinzufügen.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>So deaktivieren Sie die Standardoption zum Erstellen von Anwendungsregistrierungen oder Erteilen der Einwilligung für Anwendungen

1. Melden Sie sich bei Ihrer Azure AD-Organisation mit einem Konto an, das für die Rolle „Globaler Administrator“ in der Azure AD-Organisation berechtigt ist.
1. Legen Sie eine der folgenden Einstellungen oder beide fest:

    - Legen Sie auf der Seite [„Benutzereinstellungen“ für Ihre Organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) die Option **Benutzer können Anwendungen registrieren** auf „Nein“ fest. Dadurch wird die Standardoption für Benutzer zum Erstellen von Anwendungsregistrierungen deaktiviert.
    - Legen Sie unter [Benutzereinstellungen – Unternehmensanwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) die Einstellung **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** auf „Nein“ fest. Dadurch wird die Standardfunktion für Benutzer deaktiviert, Anwendungen den Zugriff auf Unternehmensdaten in ihrem Namen zu gestatten.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Erteilen einzelner Berechtigungen zum Erstellen von Anwendungen und Erteilen der Einwilligung für Anwendungen bei deaktivierter Standardfunktion

Weisen Sie die Rolle „Anwendungsentwickler“ zu, um die Berechtigung zum Erstellen von Anwendungsregistrierungen zu erteilen, wenn die Einstellung **Benutzer können Anwendungen registrieren** auf „Nein“ festgelegt ist. Diese Rolle erteilt auch die Berechtigung zur Zustimmung im eigenen Namen, wenn die Einstellung **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** auf „Nein“ festgelegt ist.

## <a name="assign-application-owners"></a>Zuweisen von Anwendungsbesitzern

Die Zuweisung von Besitzern ist eine einfache Möglichkeit, die Berechtigung zu erteilen, alle Aspekte der Azure AD-Konfiguration für eine bestimmte Anwendungsregistrierung oder Unternehmensanwendung zu verwalten. Weitere Informationen finden Sie unter [Zuweisen von Unternehmensanwendungsbesitzern](../manage-apps/assign-app-owners.md).

## <a name="assign-built-in-application-admin-roles"></a>Zuweisen von integrierten Anwendungsadministratorrollen

Azure AD umfasst eine Reihe integrierter Administratorrollen zum Gewähren des Zugriffs zum Verwalten der Konfiguration in Azure AD für alle Anwendungen. Die Verwendung dieser Rollen wird empfohlen, um IT-Experten Zugriff zum Verwalten von umfassenden Berechtigungen für die Anwendungskonfiguration zu gewähren, ohne Zugriff auf die Verwaltung anderer Bereiche von Azure AD zu gewähren, die nicht mit der Anwendungskonfiguration zusammenhängen.

- Anwendungsadministrator: Benutzer mit dieser Rolle können alle Aspekte von Unternehmensanwendungen, Anwendungsregistrierungen und Anwendungsproxyeinstellungen erstellen und verwalten. Diese Rolle ermöglicht auch die Zustimmung zu delegierten Berechtigungen und Anwendungsberechtigungen, mit Ausnahme von Berechtigungen für Microsoft Graph. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.
- Cloudanwendungsadministrator: Benutzer mit dieser Rolle haben die gleichen Berechtigungen wie die Rolle des Anwendungsadministrators, mit Ausnahme der Möglichkeit, den Anwendungsproxy zu verwalten. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.

Weitere Informationen und eine Beschreibung dieser Rollen finden Sie unter [Integrierte Rollen in Azure AD](permissions-reference.md).

Befolgen Sie die Anweisungen in der Anleitung zum [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md), um die Rollen „Anwendungsadministrator“ oder „Cloudanwendungsadministrator“ zuzuweisen.

> [!IMPORTANT]
> Anwendungsadministratoren und Cloudanwendungsadministratoren können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Identität der Anwendung zu wechseln. Die Anwendung umfasst möglicherweise Berechtigungen, die in Bezug auf die Berechtigungen der Administratorrolle Rechteerweiterungen darstellen. Beim Wechseln der Identität der Anwendung kann ein Administrator in dieser Rolle abhängig von den Berechtigungen der Anwendung potenziell Benutzer oder andere Objekte erstellen oder aktualisieren.
> Keine dieser Rollen gewährt die Fähigkeit zum Verwalten der Einstellungen des bedingten Zugriffs.

## <a name="create-and-assign-a-custom-role-preview"></a>Erstellen und Zuweisen einer benutzerdefinierten Rolle (Vorschau)

Die Erstellung von benutzerdefinierten Rollen und deren Zuweisung erfolgen in zwei separaten Schritten:

- [Erstellen Sie eine benutzerdefinierte *Rollendefinition*](custom-create.md), und [fügen Sie ihr Berechtigungen aus einer vordefinierten Liste hinzu](custom-available-permissions.md). Dabei handelt es sich um die gleichen Berechtigungen, die in den integrierten Rollen verwendet werden.
- [Erstellen Sie eine *Rollenzuweisung*](custom-assign-powershell.md), um die benutzerdefinierte Rolle zuzuweisen.

Durch diese separaten Schritte können Sie eine einzelne Rollendefinition erstellen und dann für verschiedene *Bereiche* mehrmals zuzuweisen. Eine benutzerdefinierte Rolle kann im organisationsweiten Bereich oder im Bereich eines einzelnen Azure AD-Objekts zugewiesen werden. Ein Beispiel für einen Objektbereich ist eine einzelne Anwendungsregistrierung. Durch Verwendung verschiedener Bereiche kann die gleiche Rollendefinition Sally für alle Anwendungsregistrierungen in der Organisation und dann Naveen nur für die Anwendungsregistrierung der Contoso-Spesenabrechnungen zugewiesen werden.

Tipps für die Erstellung und Verwendung von benutzerdefinierten Rollen für die Delegierung der Anwendungsverwaltung:
- Benutzerdefinierte Rollen gewähren nur Zugriff auf den Blättern für die aktuellen Anwendungsregistrierungen im Azure-Portal. Sie gewähren keinen Zugriff auf den Blättern für Legacyanwendungsregistrierungen.
- Benutzerdefinierte Rollen gewähren keinen Zugriff auf das Azure-Portal, wenn die Benutzereinstellung [Zugriff auf Azure AD-Verwaltungsportal einschränken](../fundamentals/users-default-permissions.md) auf „Ja“ festgelegt ist.
- Anwendungsregistrierungen, auf die der Benutzer unter Verwendung von Rollenzuweisungen Zugriff hat, werden nur auf der Registerkarte „Alle Anwendungen“ auf der Seite „App-Registrierungen“ angezeigt. Auf der Registerkarte „Anwendungen mit Besitzer“ werden sie dagegen nicht angezeigt.

Weitere Informationen zu den Grundlagen von benutzerdefinierten Rollen finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md) sowie unter [Erstellen einer benutzerdefinierten Rolle](custom-create.md) und unter [Zuweisen einer Rolle](custom-assign-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

- [Anwendungsregistrierungsuntertypen und -berechtigungen](custom-available-permissions.md)
- [Integrierte Rollen in Azure AD](permissions-reference.md)
