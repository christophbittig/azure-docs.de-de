---
title: Empfohlene Sicherheitsmaßnahmen
description: Beim Verwenden von Azure Lighthouse müssen Sicherheit und Zugriffssteuerung berücksichtigt werden.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 897f09c41d550d6200cc3d78743bb3b010f2c402
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768251"
---
# <a name="recommended-security-practices"></a>Empfohlene Sicherheitsmaßnahmen

Beim Verwenden von [Azure Lighthouse](../overview.md) müssen Sicherheit und Zugriffssteuerung berücksichtigt werden. Benutzer in Ihrem Mandanten haben direkten Zugriff auf Kundenabonnements und Ressourcengruppen, weshalb Sie Maßnahmen ergreifen sollten, um die Sicherheit Ihres Mandanten aufrechtzuerhalten. Außerdem sollten Sie sicherstellen, dass Sie nur den Zugriff zulassen, der für die effektive Verwaltung der Ressourcen ihrer Kunden erforderlich ist. Dieses Thema enthält Empfehlungen, die Ihnen dabei helfen sollen.

> [!TIP]
> Diese Empfehlungen gelten auch für [Unternehmen, die mehrere Mandanten mit Azure Lighthouse verwalten](enterprise.md).

## <a name="require-azure-ad-multi-factor-authentication"></a>Anfordern von Azure AD Multi-Factor Authentication

[Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (auch als zweistufige Überprüfung bezeichnet) trägt dazu bei, Angreifer daran zu hindern, Zugriff auf ein Konto zu erlangen, indem mehrere Authentifizierungsschritte erforderlich sind. Sie sollten Azure AD Multi-Factor Authentication für alle Benutzer in Ihrem Verwaltungsmandanten anfordern, einschließlich Benutzer, die Zugriff auf delegierte Kundenressourcen haben werden.

Es wird empfohlen, dass Sie Ihre Kunden auffordern, Azure AD Multi-Factor Authentication auch in ihren Mandanten zu implementieren.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Zuweisen von Berechtigungen an Gruppen unter Verwendung des Prinzips der geringsten Rechte

Um die Verwaltung zu vereinfachen, verwenden Sie Azure AD-Gruppen (Azure Active Directory) für jede Rolle, die zum Verwalten der Ressourcen Ihrer Kunden erforderlich ist. Auf diese Weise können Sie der Gruppe einzelne Benutzer nach Bedarf hinzufügen oder diese daraus entfernen, anstatt jedem Benutzer Berechtigungen direkt zuzuweisen.

> [!IMPORTANT]
> Um Berechtigungen für eine Azure AD-Gruppe hinzuzufügen, muss der **Gruppentyp** auf **Sicherheit** festgelegt werden. Diese Option wird bei der Erstellung der Gruppe ausgewählt. Weitere Informationen dazu finden Sie in [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Beachten Sie beim Erstellen Ihrer Berechtigungsstruktur, dass Sie das Prinzip der geringsten Rechte befolgen, damit Benutzer nur über die Berechtigungen verfügen, die zum Durchführen Ihrer Aufgaben erforderlich sind, um die Wahrscheinlichkeit von unbeabsichtigten Fehlern zu verringern.

Beispielsweise könnten Sie eine Struktur wie die folgende verwenden:

|Gruppenname  |type  |principalId  |Rollendefinition  |Rollendefinitions-ID  |
|---------|---------|---------|---------|---------|
|Architekten     |Benutzergruppe         |\<principalId\>         |Mitwirkender         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Bewertung     |Benutzergruppe         |\<principalId\>         |Leser         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM-Spezialisten     |Benutzergruppe         |\<principalId\>         |VM-Mitwirkender         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Dienstprinzipalname (SPN)         |\<principalId\>         |Mitwirkender         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Nachdem Sie diese Gruppen erstellt haben, können Sie Benutzer nach Bedarf zuweisen. Fügen Sie nur die Benutzer hinzu, die wirklich Zugriff benötigen. Achten Sie darauf, dass Sie die Gruppenmitgliedschaft regelmäßig überprüfen und alle Benutzer entfernen, die nicht mehr angemessen oder erforderlich sind.

Bedenken Sie, dass jede Gruppe (bzw. jeder Benutzer oder Dienstprinzipal), wenn Sie [das Onboarding von Kunden über ein öffentliches verwaltetes Dienstangebot durchführen](../how-to/publish-managed-services-offers.md), die Sie einschließen, über dieselben Berechtigungen für jeden Kunden verfügt, der den Plan kauft. Um verschiedene Gruppen für die Arbeit mit den einzelnen Kunden zuzuweisen, müssen Sie einen gesonderten privaten Plan veröffentlichen, der exklusiv für jeden Kunden ist, oder Sie müssen Kunden einzeln aufnehmen, indem Sie Azure Resource Manager-Vorlagen verwenden. Beispielsweise könnten Sie einen öffentlichen Plan veröffentlichen, der nur über sehr eingeschränkten Zugriff verfügt, und dann mit dem Kunden direkt zusammenarbeiten, um dessen Ressourcen für zusätzlichen Zugriff zu integrieren, indem Sie eine angepasste Azure Resource Manager-Vorlage verwenden, die nach Bedarf zusätzlichen Zugriff gewährt.

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie die [Informationen zur Sicherheitsbaseline](../security-baseline.md), um mehr darüber zu erfahren, inwiefern Anweisungen von Azure Security Benchmark für Azure Lighthouse gelten.
- [Bereitstellen von Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](cross-tenant-management-experience.md).
