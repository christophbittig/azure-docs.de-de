---
title: Verwalten des Zugriffs auf Apps
titleSuffix: Azure AD
description: Erläutert, wie Azure Active Directory es Organisationen ermöglicht, die Apps festzulegen, auf die der jeweilige Benutzer Zugriff hat.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 0f162e7c14fc2f4c9d123c5beca6a76dc9e6a072
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553096"
---
# <a name="manage-access-to-an-application"></a>Verwalten des Zugriffs auf eine Anwendung

Die fortwährende Zugriffsverwaltung sowie die Nutzungsauswertung und Berichterstellung bleiben auch nach Integration einer Anwendung in das Identitätssystem der Organisation eine Herausforderung. In vielen Fällen müssen IT-Administratoren oder der Helpdesk eine ständige aktive Rolle bei der Verwaltung des Zugriffs auf Ihre Apps ausüben. Manchmal erfolgt die Zuweisung durch das allgemeine oder abteilungsinterne IT-Team. Oft soll die Zuweisungsentscheidung an einen kommerziellen Entscheidungsträger delegiert werden, da die IT-Abteilung ohnehin dessen Genehmigung einholen muss.  

Andere Organisationen investieren in die Integration in ein vorhandenes automatisiertes Identitäts- und Zugriffsverwaltungssystem wie rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) oder attributbasierte Zugriffssteuerung (Attribute-Based Access Control, ABAC). Sowohl die Integration als auch die Entwicklung der Regeln sind häufig spezifisch und kostspielig. Auch die Überwachung oder Berichterstattung stellt bei beiden Verwaltungsansätzen eine eigene Investition dar, die komplex und teuer ist.

## <a name="how-does-azure-active-directory-help"></a>In welcher Weise hilft Azure Active Directory?

Azure AD unterstützt die weitreichende Zugriffsverwaltung für konfigurierte Anwendungen, sodass Organisationen mühelos geeignete Zugriffsrichtlinien einrichten können, die von Anwendungsfällen mit automatischer, attributbasierter Zuweisung (ABAC- oder RBAC-Szenarios) über Delegierung bis hin zur Verwaltung durch den Administrator reichen. Mit Azure AD lassen sich ohne Aufwand komplexe Richtlinien einrichten, bei denen für eine gegebene Anwendung mehrere Verwaltungsmodelle kombiniert werden, und einmal definierte Verwaltungsregeln lassen sich sogar auf weitere Anwendungen mit demselben Benutzerkreis ausweiten.

Bei Azure AD ist die Berichterstellung über Nutzung und Zuweisung vollständig integriert, sodass Administratoren Berichte über den Zuweisungszustand, Zuweisungsfehler und sogar die Nutzung leicht anfertigen können.

### <a name="assigning-users-and-groups-to-an-app"></a>Zuweisen von Benutzern und Gruppen zu einer App

Die Anwendungszuweisung von Azure AD konzentriert sich auf zwei primäre Zuordnungsmodi:

* **Einzelne Zuweisung** : Ein IT-Administrator mit den Verzeichnisberechtigungen eines globalen Administrators kann einzelne Benutzerkonten auswählen und ihnen Zugriff auf die Anwendung gewähren.

* **Gruppenbasierte Zuweisung (setzt Azure AD Premium P1 oder P2 voraus)** : Ein IT-Administrator mit der Verzeichnisberechtigung „Globaler Administrator“ kann der Anwendung eine Gruppe zuweisen. Die Zugriffsmöglichkeit bestimmter Benutzer ergibt sich daraus, ob diese zum Zeitpunkt des Zugriffs auf die Anwendung Mitglied der festgelegten Gruppe sind. Mit anderen Worten: Ein Administrator kann im Grunde folgende Zuweisungsregel erstellen: „Jedes derzeitige Mitglied der zugewiesenen Gruppe hat Zugriff auf die Anwendung“. Mit dieser Zuweisungsoption können Administratoren alle zur Verfügung stehenden Optionen zur Azure AD-Gruppenverwaltung nutzen, einschließlich [attributbasierter dynamischer Gruppen](../fundamentals/active-directory-groups-create-azure-portal.md), externer Systemgruppen (z.B. lokale Active Directory- oder Workday-Instanz) oder Gruppen mit Administrator- bzw. mit Self-Service-Verwaltung. Eine einzelne Gruppe kann problemlos mehreren Apps zugewiesen werden, sodass sichergestellt wird, dass Anwendungen mit Zuweisungsaffinität dieselben Zuweisungsregeln verwenden, um die Verwaltungskomplexität insgesamt zu verringern.

>[!NOTE]
>Derzeit werden Gruppenmitgliedschaften für die gruppenbasierte Zuordnung zu Anwendungen nicht unterstützt.

Durch Einsatz dieser beiden Zuweisungsmodi können Administratoren jeden gewünschten Ansatz zur Zuweisungsverwaltung realisieren.

### <a name="requiring-user-assignment-for-an-app"></a>Anfordern der Benutzerzuweisung für eine App

Bei bestimmten Anwendungstypen haben Sie die Möglichkeit, die Zuweisung von Benutzern zur App als Anforderung festzulegen. Dadurch können sich außer den Benutzern, die Sie der Anwendung explizit zuweisen, keine Benutzer anmelden. Bei folgenden Anwendungstypen wird diese Option unterstützt:

* Anwendungen, die für das einmalige Anmelden (SSO) im Verbund mit SAML-basierter Authentifizierung konfiguriert sind
* Anwendungsproxyanwendungen mit Azure Active Directory-Vorauthentifizierung
* Anwendungen, die auf der Azure AD-Anwendungsplattform bei Verwendung der OAuth 2.0/OpenID Connect-Authentifizierung erstellt werden (nachdem ein Benutzer oder Administrator seine Zustimmung für die Anwendung erteilt hat). Bestimmte Unternehmensanwendungen bieten mehr Kontrolle darüber, wer sich anmelden darf.

Wenn eine Benutzerzuweisung erforderlich ist, können sich nur die Benutzer anmelden, die Sie der Anwendung zuweisen (durch direkte Benutzerzuweisung oder basierend auf der Gruppenmitgliedschaft). Sie können im Portal „Meine Apps“ oder über einen direkten Link auf die App zugreifen.

Wenn die Benutzerzuweisung nicht erforderlich ist, wird die App nicht zugewiesenen Benutzern nicht in „Meine Apps“ angezeigt. Sie können sich jedoch weiterhin bei der Anwendung selbst anmelden (auch als SP-initiierte Anmeldung bezeichnet) oder auf der Seite **Eigenschaften** der Anwendung die **Benutzerzugriffs-URL** verwenden (auch als IDP-initiierte Anmeldung bezeichnet). Weitere Informationen zum Anfordern von Benutzerzuweisungskonfigurationen finden Sie unter [Konfigurieren einer Anwendung](add-application-portal-configure.md).

Diese Einstellung hat keine Auswirkung darauf, ob eine Anwendung in „Meine Apps“ angezeigt wird. Anwendungen werden im Zugriffsbereich „Meine Apps“ von Benutzern angezeigt, sobald Sie der Anwendung einen Benutzer oder eine Gruppe zugewiesen haben.

> [!NOTE]
> Wenn eine Anwendung eine Zuweisung erfordert, ist die Benutzereinwilligung für diese Anwendung nicht zulässig. Dies gilt auch dann, wenn die Benutzereinwilligung für diese App andernfalls zugelassen worden wäre. Vergewissern Sie sich, dass Sie für Apps, die eine Zuweisung erfordern, eine [mandantenweite Administratoreinwilligung erteilen](../manage-apps/grant-admin-consent.md).

Bei einigen Anwendungen ist die Option zum Anfordern der Benutzerzuweisung in den Anwendungseigenschaften nicht verfügbar. In diesen Fällen können Sie mit PowerShell die Eigenschaft „appRoleAssignmentRequired“ auf dem Dienstprinzipal festlegen.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Festlegen der Benutzerumgebung für den Zugriff auf Apps

Azure AD bietet [mehrere anpassbare Möglichkeiten zum Bereitstellen von Anwendungen](end-user-experiences.md) für Endbenutzer in Ihrer Organisation:

* „Meine Apps“ in Azure AD
* Microsoft 365-Anwendungsstartprogramm
* Direkte Anmeldung bei Verbund-Apps (service-pr)
* Deep-Links zu verbundenen, kennwortbasierten oder vorhandene Apps

Sie können festlegen, ob Benutzer, die einer Unternehmens-App zugewiesen sind, diese in „Meine Apps“ und im Microsoft 365-Anwendungsstartprogramm sehen können.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Beispiel: Komplexe Anwendungszuweisungen mit Azure AD

Betrachten Sie eine Anwendung wie Salesforce. In vielen Organisationen wird Salesforce in erster Linie von den Marketing- und Vertriebsteams verwendet. Häufig haben Mitglieder des Marketingteams umfassende Berechtigungen für den Zugriff auf Salesforce, während Mitglieder des Vertriebsteams nur beschränkten Zugriff haben. In vielen Fällen hat ein großer Personenkreis von Information-Workern eingeschränkten Zugriff auf die Anwendung. Ausnahmen von dieser Regel kommen erschwerend hinzu. Häufig ist es Sache der Marketing- oder Vertriebsleitung, Benutzern Zugriff zu gewähren oder ihre Rollen unabhängig von allgemeinen Regeln zu ändern.

Mit Azure AD können Anwendungen wie Salesforce beispielsweise für einmaliges Anmelden (SSO) und automatisierte Bereitstellung vorkonfiguriert werden. Sobald die Anwendung konfiguriert ist, kann der Administrator die einmalige Aktion zum Erstellen und Zuweisen der entsprechenden Gruppen übernehmen. In diesem Beispiel kann ein Administrator die folgenden Zuweisungen vornehmen:

* [Dynamische Gruppen](../fundamentals/active-directory-groups-create-azure-portal.md) lassen sich durch Verwendung von Attributen wie Abteilung oder Rolle derart definieren, dass sie alle Mitglieder der Vertriebs- und Marketingteams abbilden:
  
  * Alle Mitglieder von Marketinggruppen würden in diesem Fall der Rolle „Marketing“ in Salesforce zugewiesen.
  * Alle Mitglieder von Vertriebsteamgruppen würden der Rolle „Vertrieb“ in Salesforce zugewiesen werden. In einem Verfeinerungsschritt könnten mehrere Gruppen verwendet werden, die regionale, anderen Salesforce-Gruppen zugewiesene Vertriebsteams darstellen.

* Zum Aktivieren des Ausnahmemechanismus könnte für jede Rolle eine Self-Service-Gruppe erstellt werden. Die Gruppe „Salesforce Marketingausnahme“ kann beispielsweise als Self-Service-Gruppe erstellt werden. Diese Gruppe kann der Salesforce-Marketingrolle zugewiesen und das Leadershipteam der Marketingabteilung als deren Besitzer festgelegt werden. Mitglieder des Leadershipteams der Marketingabteilung könnten somit Benutzer hinzufügen oder entfernen, eine Beitrittsrichtlinie einrichten oder sogar einzelne Beitrittsanfragen genehmigen oder verweigern. Dieser Mechanismus wird auch durch ein für Information-Worker angemessenes Verfahren begünstigt, bei dem keine spezielle Schulung für Besitzer oder Mitglieder erforderlich ist.

In diesem Fall werden alle zugewiesenen Benutzer automatisch für Salesforce bereitgestellt. Da sie unterschiedlichen Gruppen hinzugefügt werden, werden die Rollenzuweisungen in Salesforce aktualisiert. Die Benutzer können Salesforce über „Meine Apps“, Office-Webclients oder durch Aufrufen der Salesforce-Anmeldeseite innerhalb der Organisation ermitteln und darauf zugreifen. Administratoren können mithilfe von Azure AD-Berichten den Nutzungs- und Zuweisungsstatus leicht anzeigen.

Administratoren können den [bedingten Zugriff von Azure AD](../conditional-access/concept-conditional-access-users-groups.md) einsetzen, um Zugriffsrichtlinien für bestimmte Rollen festzulegen. In diesen Richtlinien kann enthalten sein, ob der Zugriff von außerhalb der Unternehmensumgebung erlaubt ist, und sogar, ob für den Zugriff die mehrstufige Authentifizierung oder bestimmte Geräteanforderungen für verschiedene Anwendungsfälle zur Anwendung kommen.

## <a name="access-to-microsoft-applications"></a>Zugriff auf Microsoft-Anwendungen

Microsoft-Anwendungen (z. B. Exchange, SharePoint, Yammer usw.) werden etwas anders zugewiesen und verwaltet wie SaaS-Anwendungen von Drittanbietern oder andere Anwendungen, die Sie für einmaliges Anmelden mit Azure AD integrieren.

Es gibt drei Hauptmethoden, über die ein Benutzer Zugriff auf eine von Microsoft veröffentlichte Anwendung erhalten kann.

* Für Anwendungen in Microsoft 365 oder anderen kostenpflichtigen Sammlungen erhalten Benutzer Zugriff über eine **Lizenzzuweisung** direkt in ihrem Benutzerkonto oder über eine Gruppe mithilfe der Funktion für gruppenbasierte Lizenzzuweisung.
* Bei Anwendungen, die Microsoft oder ein Drittanbieter kostenlos für alle Benutzer veröffentlicht, erhalten Benutzer möglicherweise Zugriff über die [Benutzerzustimmung](configure-user-consent.md). Die Benutzer melden sich mit ihrem Geschäfts-, Schul- oder Unikonto von Azure AD bei der Anwendung an und gewähren dieser den Zugriff auf eine begrenzte Menge von Daten in ihrem Konto.

* Bei Anwendungen, die Microsoft oder ein Drittanbieter kostenlos für alle Benutzer veröffentlicht, können Benutzer auch Zugriff über eine [Administratorzustimmung](manage-consent-requests.md) erhalten. Dies bedeutet, dass ein Administrator festgelegt hat, dass die Anwendung von allen Personen in seiner Organisation verwendet werden kann. Daher meldet er sich mit einem globalen Administratorkonto bei der Anwendung an und gewährt allen Benutzer in der Organisation Zugriff.

Bei einigen Anwendungen werden diese Methoden kombiniert. Bestimmte Microsoft-Anwendungen gehören beispielsweise zu einem Microsoft 365-Abonnement, erfordern aber trotzdem eine Einwilligung.

Benutzer können über ihre Office 365-Portale auf Microsoft 365-Anwendungen zugreifen. Über den Schalter [Office 365-Sichtbarkeit](hide-application-from-user-portal.md) in den **Benutzereinstellungen** Ihres Verzeichnisses können Sie Microsoft 365-Anwendungen auch in „Meine Apps“ anzeigen oder ausblenden.

Wie bei Unternehmens-Apps können Sie bestimmten Microsoft-Anwendungen über das Azure-Portal oder mit PowerShell (wenn die Portaloption nicht verfügbar ist) [Benutzer zuweisen](assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Nächste Schritte

* [Schützen von Apps durch bedingten Zugriff](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Self-Service-Gruppenverwaltung/SSAA](../enterprise-users/groups-self-service-management.md)
