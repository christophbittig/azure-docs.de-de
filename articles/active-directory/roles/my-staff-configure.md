---
title: Delegieren der Benutzerverwaltung über das Portal „Meine Mitarbeiter“ – Azure AD | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Benutzerverwaltung mit „Meine Mitarbeiter“ und mit Verwaltungseinheiten delegieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d3ec7c9786a9b5d3774c05669ae6d51ad443e71d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339916"
---
# <a name="manage-your-users-with-my-staff"></a>Verwalten Ihrer Benutzer über das Portal „Meine Mitarbeiter“

Über das Portal „Meine Mitarbeiter“ können Sie Berechtigungen an Führungspersonen wie eine Filial- oder Teamleitung delegieren. Diese können dann sicherstellen, dass die Mitarbeiter Zugriff auf ihre Azure AD-Konten haben. Anstatt einen zentralen Helpdesk einzurichten, können Organisationen häufige Aufgaben wie das Zurücksetzen von Kennwörtern oder das Ändern von Telefonnummern an eine Teamleitung vor Ort delegieren. Bei Verwendung von „Meine Mitarbeiter“ kann ein Benutzer, der nicht auf sein Konto zugreifen kann, ohne die Hilfe des Helpdesks oder der IT-Abteilung mit wenigen Klicks den Zugriff wiedererlangen.

Bevor Sie das Portal „Meine Mitarbeiter“ für Ihre Organisation konfigurieren, sollten Sie diese Dokumentation sowie die [Benutzerdokumentation](../user-help/my-staff-team-manager.md) lesen, damit Sie mit der Funktionsweise des Portals und dessen Auswirkungen auf Ihre Benutzer vertraut sind. Sie können die Benutzerdokumentation verwenden, um Ihre Benutzer zu schulen und auf die neue Oberfläche vorzubereiten. So sorgen Sie für einen erfolgreichen Rollout.

## <a name="how-my-staff-works"></a>Funktionsweise von „Meine Mitarbeiter“

Das Portal „Meine Mitarbeiter“ basiert auf Verwaltungseinheiten. Hierbei handelt es sich um Ressourcencontainer, mit denen sich die administrativen Berechtigungen einer zugewiesenen Rolle einschränken lassen. Weitere Informationen finden Sie unter [Verwalten von Verwaltungseinheiten in Azure Active Directory (Vorschau)](administrative-units.md). Die Verwaltungseinheiten im Portal „Meine Mitarbeiter“ können eine Gruppe von Benutzern in einem Geschäft oder einer Abteilung enthalten. Anschließend kann einem Teammanager eine Verwaltungsrolle zugewiesen werden, die für mindestens eine Verwaltungseinheit gilt.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.

  * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ihr Abonnement muss einem Azure Active Directory-Mandanten zugeordnet sein.

  * [Erstellen Sie einen Azure Active Directory-Mandanten](../fundamentals/sign-up-organization.md), oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto](../fundamentals/active-directory-how-subscriptions-associated-directory.md), sofern erforderlich.
* Sie benötigen Berechtigungen als *globaler Administrator* in Ihrem Azure AD-Mandanten, um die SMS-basierte Authentifizierung zu aktivieren.
* Jeder Benutzer, der sich gemäß der Richtlinie zu den Authentifizierungsmethoden per SMS authentifizieren kann, muss über eine Lizenz verfügen, selbst wenn er sie nicht nutzt. Jeder aktivierte Benutzer muss über eine der folgenden Azure AD- oder Microsoft 365-Lizenzen verfügen:

  * [Azure AD Premium P1 oder P2](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)
  * [Microsoft 365 F1 oder F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 oder E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) oder [Microsoft 365 E3 oder E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Aktivieren von „Meine Mitarbeiter“

Nachdem Sie die Verwaltungseinheiten konfiguriert haben, können Sie diesen Bereich auf Ihre Benutzer anwenden, die auf das Portal „Meine Mitarbeiter“ zugreifen. Nur Benutzer, denen eine Administratorrolle zugewiesen ist, können auf „Meine Mitarbeiter“ zugreifen. Führen Sie die folgenden Schritte aus, um „Meine Mitarbeiter“ zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) als Benutzeradministrator an.

1. Wählen Sie **Azure Active Directory** > **Benutzereinstellungen** > **Vorschauen für Benutzerfeatures** > **Vorschaueinstellungen für Benutzerfeatures verwalten** aus.

1. Unter **Administratoren können auf „Meine Mitarbeiter“ zugreifen** können Sie dies für alle Benutzer, ausgewählte Benutzer oder keine Benutzer aktivieren.

> [!Note]
> Nur Benutzer, denen eine Administratorrolle zugewiesen wurde, können auf „Meine Mitarbeiter“ zugreifen. Wenn Sie „Meine Mitarbeiter“ für einen Benutzer aktivieren, dem keine Administratorrolle zugewiesen ist, kann er nicht auf „Meine Mitarbeiter“ zugreifen.

## <a name="conditional-access"></a>Bedingter Zugriff

Sie können das Portal „Meine Mitarbeiter“ mit der Azure AD-Richtlinie für bedingten Zugriff schützen. Verwenden Sie sie für Aufgaben wie die Anforderung einer mehrstufigen Authentifizierung vor dem Zugriff auf „Meine Mitarbeiter“.

Wir empfehlen dringend, dass Sie „Meine Mitarbeiter“ mit [Azure AD-Richtlinien für bedingten Zugriff](../conditional-access/index.yml) schützen. Zum Anwenden einer solchen Richtlinie auf das Portal „Meine Mitarbeiter“ müssen Sie zunächst die Website „Meine Mitarbeiter“ für einige Minuten aufrufen, damit der Dienstprinzipal automatisch in Ihrem Mandanten für den bedingten Zugriff bereitgestellt wird.

Der Dienstprinzipal wird angezeigt, wenn Sie eine Richtlinie für den bedingten Zugriff erstellen, die für die Cloudanwendung „Meine Mitarbeiter“ gilt.

![Erstellen einer Richtlinie für bedingten Zugriff für die Anwendung „Meine Mitarbeiter“](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Verwenden von „Meine Mitarbeiter“

Wenn ein Benutzer „Meine Mitarbeiter“ aufruft, werden die Namen der [Verwaltungseinheiten](administrative-units.md) angezeigt, für die er über Administratorrechte verfügt. In der [Benutzerdokumentation zu „Meine Mitarbeiter“](../user-help/my-staff-team-manager.md) verwenden wir den Begriff „Standort“ für Verwaltungseinheiten. Wenn die Berechtigungen eines Administrators nicht auf bestimmte Verwaltungseinheiten festgelegt sind, gelten sie für die gesamte Organisation. Nach dem Aktivieren von „Meine Mitarbeiter“ können die Benutzer, die zugeordnet wurden und denen eine Administratorrolle zugewiesen wurde, unter [https://mystaff.microsoft.com](https://mystaff.microsoft.com) darauf zugreifen. Sie können eine Verwaltungseinheit auswählen, um die darin enthaltenen Benutzer anzuzeigen, und dann einen Benutzer auswählen sowie sein Profil öffnen.

## <a name="reset-a-users-password"></a>Zurücksetzen des Kennworts für einen Benutzer

Bevor Sie Kennwörter für lokale Benutzer zurücksetzen können, müssen Sie die folgenden Voraussetzungen erfüllen. Ausführliche Hinweise zu den Voraussetzungen finden Sie im Tutorial [Aktivieren der Self-Service-Kennwortzurücksetzung](../authentication/tutorial-enable-sspr-writeback.md).

* Konfigurieren der Berechtigungen für das Kennwortrückschreiben
* Aktivieren des Kennwortrückschreibens in Azure AD Connect
* Aktivieren des Kennwortrückschreibens bei der Self-Service-Kennwortzurücksetzung in Azure AD

Die folgenden Rollen verfügen über die Berechtigung zum Zurücksetzen des Kennworts eines Benutzers:

* [Authentifizierungsadministrator](permissions-reference.md#authentication-administrator)
* [Privilegierter Authentifizierungsadministrator](permissions-reference.md#privileged-authentication-administrator)
* [Globaler Administrator](permissions-reference.md#global-administrator)
* [Helpdeskadministrator](permissions-reference.md#helpdesk-administrator)
* [Benutzeradministrator](permissions-reference.md#user-administrator)
* [Kennwortadministrator](permissions-reference.md#password-administrator)

Öffnen Sie in **Meine Mitarbeiter** das Profil eines Benutzers. Wählen Sie **Kennwort zurücksetzen** aus.

* Falls es sich um einen reinen Cloudbenutzer handelt, wird ein temporäres Kennwort angezeigt, das Sie an den Benutzer weitergeben können.
* Wenn der Benutzer über eine lokale Active Directory-Instanz synchronisiert wird, können Sie ein Kennwort eingeben, das Ihre lokalen AD-Richtlinien erfüllt. Anschließend können Sie das Kennwort an den Benutzer weitergeben.

    ![Statusanzeige und Erfolgsmeldung für Kennwortzurücksetzung](./media/my-staff-configure/reset-password.png)

Der Benutzer muss sein Kennwort bei der nächsten Anmeldung ändern.

## <a name="manage-a-phone-number"></a>Verwalten einer Telefonnummer

Öffnen Sie in **Meine Mitarbeiter** das Profil eines Benutzers.

* Wählen Sie den Abschnitt **Telefonnummer hinzufügen** aus, um eine Telefonnummer für den Benutzer hinzuzufügen.
* Wählen Sie **Telefonnummer bearbeiten** aus, um die Telefonnummer zu ändern.
* Wählen Sie **Telefonnummer entfernen** aus, um die Telefonnummer für den Benutzer zu entfernen.

Je nach Ihren Einstellungen kann der Benutzer dann die von Ihnen eingerichtete Telefonnummer verwenden, um sich per SMS anzumelden, die mehrstufige Authentifizierung zu nutzen oder die Self-Service-Kennwortzurücksetzung durchzuführen.

Zum Verwalten der Telefonnummer eines Benutzers muss Ihnen eine der folgenden Rollen zugewiesen sein:

* [Authentifizierungsadministrator](permissions-reference.md#authentication-administrator)
* [Privilegierter Authentifizierungsadministrator](permissions-reference.md#privileged-authentication-administrator)
* [Globaler Administrator](permissions-reference.md#global-administrator)

## <a name="search"></a>Suchen,

Über die Suchleiste im Portal „Meine Mitarbeiter“ können Sie nach Verwaltungseinheiten und Benutzern in Ihrer Organisation suchen. Alle Verwaltungseinheiten und Benutzer in Ihrer Organisation können gesucht werden. Änderungen an Benutzern in einer Verwaltungseinheit können Sie jedoch nur vornehmen, wenn Sie über Administratorrechte verfügen.

Sie können außerdem innerhalb einer Verwaltungseinheit nach einem Benutzer suchen. Verwenden Sie hierzu die Suchleiste am oberen Rand der Benutzerliste.

## <a name="audit-logs"></a>Überwachungsprotokolle

Sie können Überwachungsprotokolle für Aktionen, die in „Meine Mitarbeiter“ durchgeführt werden, im Azure Active Directory-Portal anzeigen. Falls ein Überwachungsprotokoll aufgrund einer Aktion in „Meine Mitarbeiter“ generiert wurde, wird dies im Überwachungsereignis unter „ZUSÄTZLICHE DETAILS“ angegeben.

## <a name="next-steps"></a>Nächste Schritte

[Benutzerdokumentation zu „Meine Mitarbeiter“](../user-help/my-staff-team-manager.md)
[Dokumentation zu Verwaltungseinheiten](administrative-units.md)
