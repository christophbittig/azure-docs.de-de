---
title: Bewährte Methoden für Azure AD-Rollen - Azure Active Directory
description: Bewährte Methoden für die Verwendung von Azure Active Directory-Rollen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48d424a64df215a7506130a44d57fc45d638255d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355544"
---
# <a name="best-practices-for-azure-ad-roles"></a>Bewährte Methoden für Azure AD-Rollen

In diesem Artikel werden einige der bewährten Methoden für die Verwendung der rollenbasierten Zugriffssteuerung von Azure Active Directory (Azure AD RBAC) beschrieben. Diese bewährten Methoden leiten sich aus unseren Erfahrungen mit Azure AD RBAC und den Erfahrungen von Kunden wie Ihnen ab. Wir empfehlen Ihnen, auch unsere detaillierten Sicherheitshinweise zum [Sichern des privilegierten Zugriffs für Hybrid-und cloudbereitstellungen in Azure AD](security-planning.md)zu lesen.

## <a name="1-manage-to-least-privilege"></a>1. verwalten Sie die geringstmöglichen Berechtigungen

Beim Planen Ihrer Zugriffs Steuerungsstrategie ist es eine bewährte Vorgehensweise, die geringste Berechtigung zu verwalten. Die geringste Berechtigung bedeutet, dass Sie Ihren Administratoren genau die Berechtigung erteilen, die Sie für Ihre Arbeit benötigen. Wenn Sie Ihren Administratoren eine Rolle zuweisen, müssen Sie drei Aspekte beachten: einen bestimmten Satz von Berechtigungen für einen bestimmten Bereich für einen bestimmten Zeitraum. Vermeiden Sie es, umfangreichere Rollen in umfassenderen Bereichen zuzuweisen, auch wenn dies anfänglich bequemer erscheint. Durch das Einschränken von Rollen und Bereichen begrenzen Sie die Ressourcen, die gefährdet sind, wenn der Sicherheitsprinzipal kompromittiert wird. Azure AD RBAC unterstützt über 65 [integrierte Rollen](permissions-reference.md). Es gibt Azure AD Rollen zum Verwalten von Verzeichnisojekten wie Benutzern, Gruppen und Anwendungen sowie zum Verwalten von Microsoft 365 Diensten wie Exchange, SharePoint und InTune. Um die in Azure AD integrierten Rollen besser zu verstehen, [siehe Rollen in Azure Active Directory verstehen](concept-understand-roles.md). Wenn keine integrierte Rolle vorhanden ist, die Ihren Anforderungen entspricht, können Sie eigene [benutzerdefinierte Rollen](custom-create.md)erstellen.  
 
### <a name="finding-the-right-roles"></a>Suchen nach den richtigen Rollen

Führen Sie die folgenden Schritte aus, um die richtige Rolle zu finden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus, um die Liste der Azure AD-Rollen anzuzeigen.

1. Verwenden Sie den **Dienst** Filter, um die Liste der Rollen einzugrenzen.

    ![Seite "Rollen und Administratoren" in Azure AD mit Dienst Filter geöffnet](./media/best-practices/roles-administrators.png)

1. Weitere Informationen finden Sie in der Dokumentation [Azure AD integrierte Rollen](permissions-reference.md). Die jeder Rolle zugeordneten Berechtigungen werden zusammen zur besseren Lesbarkeit aufgeführt. Zum Verständnis der Struktur und Bedeutung von Rollenberechtigungen siehe [Wie man Rollenberechtigungen versteht](permissions-reference.md#how-to-understand-role-permissions).

1. Informationen hierzu finden Sie in der Dokumentation zur [Am wenigsten privilegierte Rolle nach Aufgabe](delegate-by-task.md).

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Verwenden Sie Privileged Identity Management, um Just-in-Time-Zugriff zu gewähren

Eines der Prinzipien der geringsten Berechtigung ist, dass der Zugriff nur für einen bestimmten Zeitraum gewährt werden sollte. Mit [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) können Sie Ihren Administratoren Just-in-Time-Zugriff gewähren. Microsoft empfiehlt, PIM in Azure AD zu aktivieren. Mit PIM kann ein Benutzer zu einem berechtigten Mitglied einer Azure AD-Rolle gemacht werden. Dort kann die Rolle bei Bedarf für einen begrenzten Zeitraum aktiviert werden. Der privilegierte Zugriff wird automatisch entfernt, wenn das Zeitfenster abläuft. Sie können [PIM-Einstellungen](../privileged-identity-management/pim-how-to-change-default-settings.md) auch so konfigurieren, dass eine Genehmigung erforderlich ist oder e-Mail-Benachrichtigungen erhalten, wenn jemand Ihre Rollenzuweisung aktiviert. Benachrichtigungen liefern eine Warnung, wenn neue Benutzer zu hoch privilegierten Rollen hinzugefügt werden. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. Aktivieren Sie die mehrstufige Authentifizierung für alle Ihre Administratorkonten

[Basierend auf unseren Studien](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984) ist Ihr Konto bei Verwendung von MultiFactor-Authentifizierung (MFA) 99,9 % weniger wahrscheinlich gefährdet. 
 
Sie können die MFA für Azure AD Rollen mit zwei Methoden aktivieren:
- [Rolleneinstellungen](../privileged-identity-management/pim-how-to-change-default-settings.md) in Privileged Identity Management
- [Bedingter Zugriff](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. Konfigurieren Sie wiederkehrende Zugriffsüberprüfungen, um nicht benötigte Berechtigungen im Laufe der Zeit zu entziehen

Zugriffsüberprüfungen ermöglichen es Unternehmen, den Zugriff von Administratoren regelmäßig zu überprüfen, um sicherzustellen, dass nur die richtigen Personen weiterhin Zugriff haben. Ein regelmäßiges Auditing Ihrer Administratoren ist aus folgenden Gründen wichtig:
- Ein böswilliger Akteur kann ein Konto kompromittieren.
- Mitarbeiter verschieben Teams in einem Unternehmen. Wenn es keine Überprüfung gibt, können sie mit der Zeit unnötige Zugriffe anhäufen.
 
Informationen zu Zugriffsüberprüfungen für Rollen finden Sie unter [Erstellen einer Zugriffsübersicht über Azure AD Rollen in PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Informationen zu Zugriffsüberprüfungen von Gruppen, denen Rollen zugewiesen sind, finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen und Anwendungen in Azure AD Zugriffsüberprüfungen](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. begrenzen Sie die Anzahl der globalen Administratoren auf weniger als 5

Als bewährte Methode empfiehlt Microsoft, dass Sie die Rolle "Globaler Administrator" **weniger als fünf** Personen in Ihrer Organisation zuweisen. Globale Administratoren haben den Schlüssel zum Königreich in der Hand, und es ist in ihrem Interesse, die Angriffsfläche gering zu halten. Wie bereits erwähnt, sollten alle diese Konten mit einer mehrstufigen Authentifizierung geschützt werden.

Wenn ein Benutzer sich für einen Microsoft-Clouddienst registriert, wird standardmäßig ein Azure AD-Mandant erstellt, und der Benutzer wird Mitglied der Rolle „Globaler Administrator“. Benutzer, denen die Rolle „Globaler Administrator“ zugewiesen ist, können alle administrativen Einstellungen in Ihrer Azure AD-Organisation lesen und ändern. Bis auf wenige Ausnahmen können globale Administratoren auch alle Konfigurationseinstellungen in Ihrer Microsoft 365-Organisation lesen und ändern. Globale Administratoren haben außerdem die Möglichkeit, Ihren Zugriff auf das Lesen von Daten zu erhöhen.

Microsoft empfiehlt Ihnen, zwei Konten für den Notfallzugriff zu verwalten, die dauerhaft der Rolle „Globaler Administrator“ zugewiesen sind. Stellen Sie sicher, dass für diese Konten nicht der gleiche MFA-Mechanismus wie für die normalen Verwaltungskonten zum Anmelden verwendet wird. Weitere Informationen finden Sie unter [Verwalten von Konten für den Notfallzugriff in Azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Verwenden Sie Gruppen für Azure AD Rollenzuweisungen, und delegieren Sie die Rollenzuweisung

Wenn Sie über ein externes Governancesystem verfügen, das Gruppen nutzt, sollten Sie Azure AD-Gruppen anstelle einzelner Benutzerrollen zuweisen. Mit wenigen Ausnahmen können globale Administratoren auch alle Konfigurationseinstellungen in Ihrer Microsoft 365-Organisation lesen und ändern.Sie können auch rollenzuweisbare Gruppen in PIM verwalten, um sicherzustellen, dass es keine ständigen Besitzer oder Mitglieder in diesen privilegierten Gruppen gibt. [Weitere Informationen finden Sie unter Verwaltungsfunktionen für Azure AD-Gruppen mit privilegiertem Zugriff](../privileged-identity-management/groups-features.md).

Sie können rollenzuweisbaren Gruppen einen Besitzer zuweisen. Der Besitzer entscheidet, wer zur Gruppe hinzugefügt oder aus ihr entfernt wird, und damit indirekt auch, wer die Rollenzuweisung erhält. Auf diese Weise kann ein globaler Administrator oder ein Administrator für privilegierte Rollen die Rollenverwaltung mit Hilfe von Gruppen auf einer rollenspezifischen Grundlage delegieren. Weitere Informationen finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. Aktivieren Sie mehrere Rollen auf einmal mit privilegierten Zugriffsgruppen

Es kann der Fall sein, dass eine Person fünf oder sechs berechtigte Zuweisungen zu Azure AD-Rollen über PIM hat. Sie müssen dann jede Rolle einzeln aktivieren, was die Produktivität verringern kann. Schlimmer noch, ihnen können auch Dutzende oder Hunderte von Azure-Ressourcen zugewiesen sein, was das Problem noch verschlimmert.
 
In diesem Fall sollten Sie [privilegierte Zugriffsgruppen](../privileged-identity-management/groups-features.md)verwenden. Erstellen Sie eine privilegierte Zugriffsgruppe und gewähren Sie ihr permanenten Zugriff auf mehrere Rollen (Azure AD und/oder Azure). Diesen Benutzer zu einem berechtigten Mitglied oder Besitzer dieser Gruppe machen. Mit nur einer Aktivierung haben sie Zugriff auf alle verlinkten Ressourcen.

![Diagramm der Gruppe mit privilegiertem Zugriff, das die gleichzeitige Aktivierung mehrerer Rollen zeigt](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Verwenden Sie Cloud-native Konten für Azure AD-Rollen

Vermeiden Sie lokale Synchronisierungskonten für Azure AD-Rollenzuweisungen. Wenn Ihr lokales Konto kompromittiert ist, kann es auch Ihre Azure AD-Ressourcen beeinträchtigen.

## <a name="next-steps"></a>Nächste Schritte

- [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](security-planning.md)
