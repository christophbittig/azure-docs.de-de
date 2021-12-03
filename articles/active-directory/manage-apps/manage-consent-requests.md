---
title: Verwalten der Einwilligung in Anwendungen und Auswerten von Einwilligungsanforderungen
description: Erfahren Sie, wie Sie Einwilligungsanforderungen verwalten, wenn die Benutzereinwilligung deaktiviert oder eingeschränkt ist, und wie Sie eine Anforderung für eine mandantenweite Administratoreinwilligung für eine Anwendung in Azure Active Directory auswerten.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: e871bfe8f6fa787a3ffffc4fa0efb2df6d920a0e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548385"
---
# <a name="manage-consent-to-applications-and-evaluate-consent-requests"></a>Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen

Microsoft empfiehlt, die [Möglichkeiten zur Benutzereinwilligung einzuschränken](../../active-directory/manage-apps/configure-user-consent.md), damit Benutzer ihre Einwilligung nur für Apps von überprüften Herausgebern und nur für von Ihnen ausgewählte Berechtigungen erteilen können. Für Apps, die diese Richtlinie nicht erfüllen, wird der Entscheidungsprozess mit dem Sicherheits- und Identitätsadministratorteam Ihrer Organisation zentralisiert.

Nachdem die Endbenutzereinwilligung deaktiviert oder eingeschränkt wurde, müssen einige wichtige Überlegungen berücksichtigt werden, um sicherzustellen, dass Ihre Organisation sicher bleibt und unternehmenskritische Anwendungen weiterhin genutzt werden können. Diese Schritte sind entscheidend, um die Auswirkungen auf das Supportteam und die IT-Administratoren Ihrer Organisation zu minimieren und gleichzeitig die Verwendung nicht verwalteter Konten in Anwendungen von Drittanbietern zu verhindern.

## <a name="process-changes-and-education"></a>Prozessänderungen und Fortbildung

 1. Aktivieren Sie ggf. den [Workflow zur Administratoreinwilligung](configure-admin-consent-workflow.md), um Benutzern zu gestatten, Administratorgenehmigungen direkt über den Einwilligungsbildschirm anzufordern.

 2. Stellen Sie sicher, dass alle Administratoren die [Berechtigungen und das Einwilligungsframework](../develop/consent-framework.md) verstehen und wissen, wie die [Einwilligungsaufforderung](../develop/application-consent-experience.md) funktioniert und wie [eine Anforderung zur mandantenweiten Administratoreinwilligung](#evaluating-a-request-for-tenant-wide-admin-consent) ausgewertet wird.
 3. Überprüfen Sie die vorhandenen Prozesse Ihrer Organisation darauf, wie Benutzer eine Administratorgenehmigung für eine Anwendung anfordern, und nehmen Sie ggf. Aktualisierungen vor. Wenn Prozesse geändert werden:
    * Aktualisieren Sie die relevante Dokumentation, die Überwachung, die Automatisierung usw.
    * Kommunizieren Sie die Prozessänderungen an alle betroffenen Benutzer, Entwickler, Supportteams und IT-Administratoren.

## <a name="auditing-and-monitoring"></a>Auditing und Überwachung

1. [Überwachen Sie Apps und erteilte Berechtigungen](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) in Ihrer Organisation, um sicherzustellen, dass nicht berechtigten oder verdächtigen Anwendungen zuvor kein Zugriff auf Daten gewährt wurde.

2. Unter [Erkennen und Korrigieren von unerlaubter Zustimmung in Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) finden Sie zusätzliche bewährte Methoden und Sicherheitsvorkehrungen gegen verdächtige Anwendungen, die eine OAuth-Einwilligung anfordern.

3. Wenn Ihre Organisation über eine geeignete Lizenz verfügt:

    * Verwenden Sie zusätzliche [Features für die OAuth-Anwendungsüberwachung in Microsoft Defender für Cloud-Apps](/cloud-app-security/investigate-risky-oauth).
    * Verwenden Sie [Azure Monitor-Arbeitsmappen für die Überwachung von Berechtigungen und Einwilligungen](../reports-monitoring/howto-use-azure-monitor-workbooks.md) und damit im Zusammenhang stehende Aktivitäten. Die Arbeitsmappe *Erkenntnisse aus Einwilligungen* bietet eine Ansicht der Apps nach der Anzahl fehlerhafter Einwilligungsanforderungen. Dies kann hilfreich sein, um Anwendungen für die Überprüfung durch die Administratoren zu priorisieren und zu entscheiden, ob ihnen eine Administratoreinwilligung gewährt werden soll.

### <a name="additional-considerations-for-reducing-friction"></a>Zusätzliche Überlegungen zur Vermeidung von Schwierigkeiten

Um die Auswirkungen auf vertrauenswürdige, unternehmenskritische Anwendungen zu minimieren, die bereits verwendet werden, sollten Sie den Anwendungen, die über eine große Anzahl von gewährten Benutzereinwilligungen verfügen, proaktiv eine Administratoreinwilligung erteilen:

1. Erstellen Sie eine Bestandsaufnahme der bereits in Ihrer Organisation hinzugefügten Apps mit hohen Nutzungszahlen basierend auf den Anmeldeprotokollen oder der Zustimmungsaktivität für Einwilligungen. Mit einem PowerShell-[Skript](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) können Sie schnell und einfach Anwendungen mit einer großen Anzahl von erteilten Benutzereinwilligungen ermitteln.

2. Evaluieren Sie die wichtigsten Anwendungen, denen bisher noch keine Administratoreinwilligung erteilt wurde.

   > [!IMPORTANT]
   > Evaluieren Sie eine Anwendung sorgfältig, bevor Sie eine mandantenweite Administratoreinwilligung erteilen, auch wenn bereits viele Benutzer in der Organisation für sich selbst eingewilligt haben.

3. Erteilen Sie für jede genehmigte Anwendung eine mandantenweite Administratoreinwilligung mithilfe einer der unten beschriebenen Methoden.

4. Denken Sie bei jeder genehmigten Anwendung an das [Einschränken des Benutzerzugriffs](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Auswerten einer Anforderung für eine mandantenweite Administratoreinwilligung

Das Erteilen einer mandantenweiten Administratoreinwilligung ist ein sensibler Vorgang.  Berechtigungen werden für die gesamte Organisation erteilt. Dies kann auch Vorgänge mit umfassenden Berechtigungen einschließen. Beispielsweise hat die Rollenverwaltung Vollzugriff auf alle Postfächer oder alle Websites und die Berechtigung für vollständige Benutzeridentitätswechsel.

Vor dem Erteilen einer mandantenweiten Administratoreinwilligung müssen Sie sicherstellen, dass Sie der Anwendung und dem Anwendungsherausgeber im Sinne der zu erteilenden Zugriffsebene vertrauen. Wenn Sie nicht sicher sind, wer die Anwendung steuert und warum die Anwendung die Berechtigungen anfordert, *erteilen Sie keine Einwilligung*.

Die folgende Liste enthält einige Empfehlungen, die Sie beim Auswerten einer Anforderung zum Erteilen einer Administratoreinwilligung beachten sollten.

* **Informieren Sie sich über die [Berechtigungen und das Einwilligungsframework](../develop/consent-framework.md) in Microsoft Identity Platform.**

* **Machen Sie sich mit dem Unterschied zwischen [delegierten Berechtigungen und Anwendungsberechtigungen](../develop/v2-permissions-and-consent.md#permission-types) vertraut.**

   Anwendungsberechtigungen ermöglichen der Anwendung den Zugriff auf die Daten für die gesamte Organisation ohne Benutzerinteraktion. Delegierte Berechtigungen ermöglichen es der Anwendung, im Namen eines Benutzers zu agieren, der zu einem bestimmten Zeitpunkt bei der Anwendung angemeldet war.

* **Informieren Sie sich über die angeforderten Berechtigungen.**

   Die von der Anwendung angeforderten Berechtigungen werden in der [Einwilligungsaufforderung](../develop/application-consent-experience.md) aufgeführt. Wenn Sie den Berechtigungstitel erweitern, wird die Beschreibung der Berechtigung angezeigt. Die Beschreibung von Anwendungsberechtigungen enthält in der Regel „ohne einen angemeldeten Benutzer“. Die Beschreibung von delegierten Berechtigungen enthält in der Regel „im Namen des angemeldeten Benutzers“. Berechtigungen für die Microsoft Graph-API werden in der [Referenz zu Microsoft Graph-Berechtigungen](/graph/permissions-reference) beschrieben. Informationen zu den von anderen APIs verfügbar gemachten Berechtigungen finden Sie in der jeweiligen Dokumentation.

   Wenn Sie die angeforderte Berechtigung nicht verstehen, *erteilen Sie keine Einwilligung*.

* **Informieren Sie sich, welche Anwendung Berechtigungen anfordert und wer die Anwendung veröffentlicht hat.**

   Nehmen Sie sich vor böswilligen Anwendungen in Acht, die versuchen, wie andere Anwendungen zu erscheinen.

   Wenn Sie die Legitimität einer Anwendung oder ihres Herausgebers anzweifeln, *erteilen Sie keine Einwilligung*. Suchen Sie stattdessen nach weiteren Bestätigung (z. B. direkt beim Anwendungsherausgeber).

* **Stellen Sie sicher, dass die angeforderten Berechtigungen den Features entsprechen, die Sie von der Anwendung erwarten.**

   Beispielsweise ist für eine Anwendung, die SharePoint-Websiteverwaltung anbietet, möglicherweise delegierter Zugriff erforderlich, um alle Websitesammlungen zu lesen. Sie benötigt jedoch nicht unbedingt Vollzugriff auf alle Postfächer oder vollständige Berechtigungen für Identitätswechsel im Verzeichnis.

   Wenn Sie vermuten, dass die Anwendung mehr Berechtigungen anfordert, als sie benötigt, *erteilen Sie keine Einwilligung*. Wenden Sie sich an den Anwendungsherausgeber, um weitere Details zu erfahren.

## <a name="granting-consent-as-an-administrator"></a>Erteilen der Einwilligung als Administrator

### <a name="granting-tenant-wide-admin-consent"></a>Erteilen einer mandantenweiten Administratoreinwilligung

Unter [Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung](grant-admin-consent.md) finden Sie ausführliche Anleitungen zum Erteilen einer mandantenweiten Administratoreinwilligung über das Azure-Portal, mithilfe von Azure AD PowerShell oder über die Einwilligungsaufforderung selbst.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Erteilen einer Einwilligung im Namen eines bestimmten Benutzers

Anstatt die Einwilligung für die gesamte Organisation zu erteilen, kann ein Administrator auch die [Microsoft Graph-API](/graph/use-the-api) verwenden, um eine Einwilligung für delegierte Berechtigungen im Namen eines einzelnen Benutzers zu erteilen. Ein ausführliches Beispiel für die Verwendung von Microsoft Graph PowerShell finden Sie unter [Zustimmung im Namen eines einzelnen Nutzers mithilfe von PowerShell erteilen](#grant-consent-on-behalf-of-a-single-user-using-powershell).

## <a name="limiting-user-access-to-applications"></a>Beschränken des Benutzerzugriffs auf Anwendungen

Der Zugriff von Benutzern auf Anwendungen kann auch dann noch eingeschränkt werden, wenn eine mandantenweite Administratoreinwilligung erteilt wurde. Weitere Informationen dazu, wie Sie eine Benutzerzuweisung zu einer Anwendung erzwingen, finden Sie unter [Methoden zum Zuweisen von Benutzern und Gruppen](./assign-user-or-group-access-portal.md).

Eine umfassendere Übersicht, einschließlich der Behandlung weiterer komplexer Szenarien, finden Sie unter [Verwenden von Azure AD für die Anwendungszugriffsverwaltung](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Deaktivieren aller zukünftigen Vorgänge für die Benutzereinwilligung für jede Anwendung

Die Deaktivierung der Benutzerzustimmung für das gesamte Verzeichnis führt dazu, dass Endbenutzer keiner Anwendung zustimmen können. Administratoren können weiterhin im Namen von Benutzern zustimmen. Weitere Informationen zur Zustimmung zu Anwendungen sowie zum Aktivieren bzw. Deaktivieren der Zustimmung finden Sie unter [Grundlegendes zur Benutzer- und Administratorzustimmung](../develop/howto-convert-app-to-be-multi-tenant.md).

Führen Sie die folgenden Schritte aus, um alle zukünftigen Vorgänge der Benutzerzustimmung in Ihrem gesamten Verzeichnis zu deaktivieren:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Wählen Sie **Unternehmensanwendungen** aus, und wählen Sie im Abschnitt **Verwalten** die Option **Benutzereinstellungen** aus.
:::image type="content" source="media/manage-consent-requests/disable-user-consent-operations.png" alt-text="Deaktivieren von Vorgängen der Benutzerzustimmung für alle Apps":::
5. Deaktivieren Sie alle zukünftigen Vorgänge der Benutzerzustimmung, indem Sie die Umschaltfläche **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** auf **Nein** festlegen und dann auf die Schaltfläche **Speichern** klicken.

## <a name="grant-consent-on-behalf-of-a-single-user-using-powershell"></a>Zustimmung im Namen eines einzelnen Benutzers mithilfe von PowerShell erteilen

Wenn ein Nutzer die Zustimmung für sich selbst erteilt, geschieht Folgendes:

1. Ein Dienstprinzipal für die Clientanwendung wird erstellt, wenn noch nicht vorhanden ist. Ein Dienstprinzipal ist die Instanz einer Anwendung oder eines Diensts in Ihrem Azure AD-Mandanten. Der Zugriff, der der App oder dem Dienst gewährt wird, ist diesem Dienstprinzipalobjekt zugeordnet.
1. Für jede API, auf die die Anwendung einen Zugriff benötigt, wird eine delegierte Berechtigungserteilung für die Zugangsberechtigungen erstellt, die von der Anwendung für diese API für den Zugriff im Namen des Nutzers benötigt werden. Eine delegierte Berechtigungserteilung autorisiert eine Anwendung, im Namen eines Nutzers auf eine API zuzugreifen, wenn sich dieser Nutzer angemeldet hat.
1. Dem Benutzer wird die Clientanwendung zugewiesen. Durch das Zuweisen der Anwendung zum Benutzer wird sichergestellt, dass die Anwendung im Portal [Meine Apps](my-apps-deployment-plan.md) für diesen Benutzer aufgelistet wird, sodass er den gewährten Zugriff überprüfen und widerrufen kann.

Um die Schritte manuell auszuführen, die dem Erteilen der Zustimmung für eine Anwendung im Namen eines Nutzers gleichkommen, benötigen Sie die folgenden Angaben:

* Die App-ID für die App, für die Sie ihre Zustimmung erteilen (diese wird als "Client-Anwendung" bezeichnet).
* Die API-Berechtigungen, die für die Client-Anwendung erforderlich sind. Sie müssen die App-ID der API und die Berechtigungs-IDs oder Anspruchswerte kennen.
* Der Nutzername oder die Objekt-ID für den Nutzer, für den der Zugriff gewährt wird.

Im folgenden Beispiel verwenden wir [Microsoft Graph PowerShell](/graph/powershell/get-started), um die drei oben aufgeführten Schritte auszuführen, um die Zustimmung im Namen eines einzelnen Nutzers zu erteilen. In diesem Beispiel ist die Client-Anwendung [Microsoft Graph Explorer](https://aka.ms/ge) und wir gewähren Zugriff auf die Microsoft Graph-API.

```powershell
# The app for which consent is being granted. In this example, we're granting access
# to Microsoft Graph Explorer, an application published by Microsoft.
$clientAppId = "de8bc8b5-d9f9-48b1-a8ad-b748da725064" # Microsoft Graph Explorer

# The API to which access will be granted. Microsoft Graph Explorer makes API 
# requests to the Microsoft Graph API, so we'll use that here.
$resourceAppId = "00000003-0000-0000-c000-000000000000" # Microsoft Graph API

# The permissions to grant. Here we're including "openid", "profile", "User.Read"
# and "offline_access" (for basic sign-in), as well as "User.ReadBasic.All" (for 
# reading other users' basic profile).
$permissions = @("openid", "profile", "offline_access", "User.Read", "User.ReadBasic.All")

# The user on behalf of who access will be granted. The app will be able to access 
# the API on behalf of this user.
$userUpnOrId = "user@example.com"

# Step 0. Connect to Microsoft Graph PowerShell. We need User.ReadBasic.All to get
#    users' IDs, Application.ReadWrite.All to list and create service principals, 
#    DelegatedPermissionGrant.ReadWrite.All to create delegated permission grants, 
#    and AppRoleAssignment.ReadWrite.All to assign an app role.
#    WARNING: These are high-privilege permissions!
Connect-MgGraph -Scopes ("User.ReadBasic.All Application.ReadWrite.All " `
                        + "DelegatedPermissionGrant.ReadWrite.All " `
                        + "AppRoleAssignment.ReadWrite.All")

# Step 1. Check if a service principal exists for the client application. 
#     If one does not exist, create it.
$clientSp = Get-MgServicePrincipal -Filter "appId eq '$($clientAppId)'"
if (-not $clientSp) {
   $clientSp = New-MgServicePrincipal -AppId $clientAppId
}

# Step 2. Create a delegated permission grant granting the client app access to the
#     API, on behalf of the user. (This example assumes that an existing delegated 
#     permission grant does not already exist, in which case it would be necessary 
#     to update the existing grant, rather than create a new one.)
$user = Get-MgUser -UserId $userUpnOrId
$resourceSp = Get-MgServicePrincipal -Filter "appId eq '$($resourceAppId)'"
$scopeToGrant = $permissions -join " "
$grant = New-MgOauth2PermissionGrant -ResourceId $resourceSp.Id `
                                     -Scope $scopeToGrant `
                                     -ClientId $clientSp.Id `
                                     -ConsentType "Principal" `
                                     -PrincipalId $user.Id

# Step 3. Assign the app to the user. This ensure the user can sign in if assignment
#     is required, and ensures the app shows up under the user's My Apps.
if ($clientSp.AppRoles | ? { $_.AllowedMemberTypes -contains "User" }) {
    Write-Warning ("A default app role assignment cannot be created because the " `
                 + "client application exposes user-assignable app roles. You must " `
                 + "assign the user a specific app role for the app to be listed " `
                 + "in the user's My Apps access panel.")
} else {
    # The app role ID 00000000-0000-0000-0000-000000000000 is the default app role
    # indicating that the app is assigned to the user, but not for any specific 
    # app role.
    $assignment = New-MgServicePrincipalAppRoleAssignedTo `
          -ServicePrincipalId $clientSp.Id `
          -ResourceId $clientSp.Id `
          -PrincipalId $user.Id `
          -AppRoleId "00000000-0000-0000-0000-000000000000"
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Fünf Schritte zum Sichern Ihrer Identitätsinfrastruktur](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)
* [Konfigurieren der Art der Benutzereinwilligung für eine Anwendung in Azure Active Directory](configure-user-consent.md)
* [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)
