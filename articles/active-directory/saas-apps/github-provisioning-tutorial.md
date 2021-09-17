---
title: 'Tutorial: Benutzerbereitstellung für GitHub – Azure AD'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung der Organisationsmitgliedschaft von Benutzern in GitHub Enterprise Cloud konfigurieren.
services: active-directory
author: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: thwimmer
ms.openlocfilehash: b8de34165c327d335bff399319852ef035a7094e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728217"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von GitHub für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in GitHub und Azure AD ausführen müssen, um die Bereitstellung einer GitHub Enterprise Cloud-Organisationsmitgliedschaft zu automatisieren.

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der [GitHub-SCIM-API](https://developer.github.com/v3/scim/), die für [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)-Kunden gemäß dem [GitHub Enterprise-Abrechnungsplan](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) zur Verfügung steht.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure Active Directory-Mandanten
* Eine in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) erstellte GitHub-Organisation (erfordert den [Abrechnungsplan für GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)).
* Ein Benutzerkonto in GitHub mit Administratorberechtigungen für die Organisation
* [Für die GitHub Enterprise Cloud-Organisation konfigurierte SAML](./github-tutorial.md)
* Stellen Sie sicher, dass der OAuth-Zugriff für Ihre Organisation wie [hier](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) beschrieben bereitgestellt wurde.
* Die SCIM-Bereitstellung in einer einzelnen Organisation wird nur unterstützt, wenn SSO auf Organisationsebene aktiviert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="assigning-users-to-github"></a>Zuweisen von Benutzern zu GitHub

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden. 

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre GitHub-App benötigen. Anschließend können Sie diese Benutzer Ihrer GitHub-App zuweisen, indem Sie diese Anweisungen befolgen:

Weitere Informationen finden Sie unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-github"></a>Wichtige Tipps zum Zuweisen von Benutzern zu GitHub

* Es wird empfohlen, GitHub einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu GitHub müssen Sie entweder die Rolle **Benutzer** oder eine andere gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Die Rolle **Standardzugriff** funktioniert nicht für die Bereitstellung. Diese Benutzer werden übersprungen.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurieren der Benutzerbereitstellung in GitHub

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD zur SCIM-Bereitstellungs-API von GitHub, um die Bereitstellung der GitHub-Organisationsmitgliedschaft zu automatisieren. Bei dieser Integration, die eine [OAuth-App](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/authorizing-oauth-apps#oauth-apps-and-organizations) nutzt, wird der Zugriff von Mitgliedern auf eine GitHub Enterprise Cloud-Organisation basierend auf der Benutzer- und Gruppenzuweisung in Azure AD hinzugefügt, verwaltet und entfernt. Wenn Benutzer [über SCIM in einer GitHub-Organisation bereitgestellt wurden](https://docs.github.com/en/free-pro-team@latest/rest/reference/scim#provision-and-invite-a-scim-user), wird an die E-Mail-Adresse des Benutzers eine E-Mail-Einladung gesendet.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten für GitHub in Azure AD

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Suchen Sie über das Suchfeld nach Ihrer GitHub-Instanz, wenn Sie GitHub bereits für einmaliges Anmelden konfiguriert haben. Wählen Sie andernfalls **Hinzufügen**, und suchen Sie im Anwendungskatalog nach **GitHub**. Wählen Sie GitHub in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre GitHub-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

   ![GitHub-Bereitstellung](./media/github-provisioning-tutorial/github1.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren**. Mit diesem Vorgang wird ein GitHub-Autorisierungsdialogfeld in einem neuen Browserfenster geöffnet. Beachten Sie, dass Sie sicherstellen müssen, dass Sie für die Autorisierung des Zugriffs genehmigt sind. Folgen Sie den [hier](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) beschriebenen Anweisungen.

6. Melden Sie sich im neuen Fenster mit Ihrem Administratorkonto bei GitHub an. Wählen Sie im daraufhin angezeigten Autorisierungsdialogfeld das GitHub-Team aus, für das Sie die Bereitstellung aktivieren möchten, und wählen Sie dann **Autorisieren**. Kehren Sie anschließend zum Azure-Portal zurück, um die Konfiguration der Bereitstellung abzuschließen.

   ![Screenshot der Anmeldeseite für GitHub.](./media/github-provisioning-tutorial/github2.png)

7. Geben Sie im Azure-Portal die **Mandanten-URL** ein, und klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer GitHub-App herstellen kann. Überprüfen Sie bei einem Verbindungsfehler, ob das GitHub-Konto über Administratorberechtigungen verfügt und die **Mandanten-URL** korrekt eingegeben wurde. Wiederholen Sie anschließend den Autorisierungsschritt. (Sie können die **Mandanten-URL** durch eine Regel ersetzen: `https://api.github.com/scim/v2/organizations/<Organization_name>`. Die Organisationen finden Sie im GitHub-Konto unter **Einstellungen** > **Organisationen**).

   ![Screenshot der Seite „Organisationen“ in GitHub.](./media/github-provisioning-tutorial/github3.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen „Bei Fehler E-Mail-Benachrichtigung senden“.

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit GitHub synchronisieren**.

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit GitHub synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in GitHub für Updatevorgänge verwendet werden. Aktivieren Sie nicht die Einstellung **Priorität für den Abgleich** für die anderen Standardattribute im Abschnitt **Bereitstellung**, da dann Fehler auftreten können. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

12. Um den Azure AD-Bereitstellungsdienst für GitHub zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

13. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die GitHub im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst ausgeführt werden. 

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
