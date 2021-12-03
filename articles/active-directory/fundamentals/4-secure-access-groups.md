---
title: Schützen des externen Zugriffs mit Gruppen in Azure Active Directory und Microsoft 365
description: Azure Active Directory und Microsoft 365-Gruppen können verwendet werden, um die Sicherheit zu erhöhen, wenn externe Benutzer auf Ihre Ressourcen zugreifen.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e6813391c8077e44e2bb09b71404242707df5a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459404"
---
# <a name="securing-external-access-with-groups"></a>Schützen des externen Zugriffs mit Gruppen 

Gruppen sind ein wesentlicher Bestandteil jeder Strategie für die Zugriffssteuerung. Azure Active Directory-Sicherheitsgruppen (Azure AD-Sicherheitsgruppen) und Microsoft 365-Gruppen können als Grundlage für das Schützen des Zugriffs auf Ressourcen verwendet werden.

Gruppen stellen die bestmögliche Grundlage für die folgenden Mechanismen für die Zugriffssteuerung dar:

* Richtlinien für bedingten Zugriff

* Zugriffspakete mit Berechtigungsverwaltung 

* Zugriff auf Microsoft 365-Ressourcen, Microsoft Teams und SharePoint-Websites

Gruppen verfügen über die folgenden Rollen:

* Besitzer: Gruppenbesitzer verwalten die Gruppeneinstellungen und die Mitgliedschaften.

* Mitglieder: Mitglieder erben die Berechtigungen und die Zugriffsrechte, die der Gruppe zugewiesen werden.

* Gäste: Gäste sind Mitglieder von außerhalb Ihrer Organisation. 

## <a name="determine-your-group-strategy"></a>Ermitteln Ihrer Gruppenstrategie

Berücksichtigen Sie beim Entwickeln Ihrer Gruppenstrategie für den Schutz des externen Zugriffs auf Ihre Ressourcen [Ihren gewünschten Sicherheitsstatus](1-secure-access-posture.md), um die folgenden Fragen beantworten zu können.

* **Wer sollte die Möglichkeit haben, Gruppen zu erstellen?** Überlegen Sie, ob nur Administratoren Gruppen erstellen können sollen oder ob dies auch für Mitarbeiter oder externe Benutzer möglich sein soll.

   * *Standardmäßig können alle Mitglieder eines Mandanten Azure AD-Sicherheitsgruppen erstellen*. 

      * Sie können den [Zugriff auf das Portal für andere Benutzer als Administratoren einschränken](../develop/howto-restrict-your-app-to-a-set-of-users.md) und die Option zum Erstellen von Gruppen in [PowerShell](../enterprise-users/groups-troubleshooting.md) deaktivieren. 

      * Darüber hinaus können Sie auch die [Self-Service-Gruppenverwaltung in Azure Active Directory einrichten](../enterprise-users/groups-self-service-management.md). 

   * *Standardmäßig können alle Benutzer Microsoft 365-Gruppen erstellen, und alle Benutzer Ihres Mandanten (intern und extern) können den Gruppen beitreten*. 

      * Sie können die [Erstellung von Microsoft 365-Gruppen für die Mitglieder einer bestimmten Sicherheitsgruppe einschränken](/microsoft-365/solutions/manage-creation-of-groups). Verwenden Sie Windows PowerShell, um diese Einstellung zu konfigurieren. 

* **Wer sollte die Möglichkeit haben, Personen für Gruppen einzuladen?** Überlegen Sie, ob das Hinzufügen von anderen Mitgliedern für alle Gruppenmitglieder oder nur für Gruppenbesitzer möglich sein soll.

* **Wer kann für Gruppen eingeladen werden?** Standardmäßig können den Gruppen externe Benutzer hinzugefügt werden. 

### <a name="assign-users-to-groups"></a>Zuweisen von Benutzern zu Gruppen

Benutzer können Gruppen sowohl manuell anhand der Benutzerattribute im Benutzerobjekt als auch anhand von anderen Kriterien zugewiesen werden. Die Zuweisung von Benutzern zu Gruppen ist nur dynamisch basierend auf ihren Attributen möglich.

Für die Zuweisung von Benutzern zu Gruppen können beispielsweise die folgenden Attribute verwendet werden:

* Position oder Abteilung

* Partnerorganisation, der ein Benutzer angehört (manuell oder über verbundene Organisationen)

* Benutzertyp (Mitglied oder Gast)

* Teilnahme an einem bestimmten Projekt (manuell)

* location

Dynamische Gruppen können entweder Benutzer oder Geräte enthalten, aber nicht beides. Sie fügen basierend auf Benutzerattributen Abfragen hinzu, um der dynamischen Gruppe Benutzer zuzuweisen. Im Beispiel unten sind Abfragen dargestellt, mit denen Benutzer der Gruppe hinzugefügt werden, falls sie Mitglieder sind (keine Gäste) und in der Finanzabteilung arbeiten.

![Screenshot: Konfigurieren von dynamischen Mitgliedschaftsregeln](media/secure-external-access/4-dynamic-membership-rules.png)

Weitere Informationen zu dynamischen Gruppen finden Sie unter [Erstellen oder Aktualisieren einer dynamischen Gruppe in Azure Active Directory](../enterprise-users/groups-create-rule.md).

### <a name="do-not-use-groups-for-multiple-purposes"></a>Kein Verwenden von Gruppen für mehrere Zwecke

Wenn Sie Gruppen für die Sicherheit oder den Zugriff auf Ressourcen nutzen, ist es wichtig, dass diese nicht mehr als einem Zweck dienen. Falls eine Gruppe zum Gewähren des Zugriffs auf Ressourcen verwendet wird, sollte sie nicht noch für weitere Zwecke eingesetzt werden. Eine Gruppe, die für allgemeine Zwecke bestimmt ist, z. B. das Definieren der Standort- oder Teammitgliedschaft, ist es nicht ratsam, sie auch zum Schützen des Zugriffs zu verwenden. 

Wir empfehlen Ihnen, eine Namenskonvention für Sicherheitsgruppen zu nutzen, bei der der Zweck eindeutig beschrieben wird. Beispiel:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Arten von Gruppen

Sowohl Azure AD-Sicherheitsgruppen als auch Microsoft 365-Gruppen können im Azure AD-Portal oder im Microsoft 365-Verwaltungsportal erstellt werden. Beide Arten können als Grundlage für das Schützen des externen Zugriffs verwendet werden:

| Überlegungen | Azure AD-Sicherheitsgruppen (manuell und dynamisch)| Microsoft 365-Gruppen |
| - | - | - |
| Was kann die Gruppe enthalten?| Benutzer<br>Gruppen<br>Dienstprinzipale<br>Geräte| Nur Benutzer |
| Wo wird die Gruppe erstellt?| Azure AD-Portal<br>Microsoft 365-Portal (falls E-Mail-Aktivierung erforderlich ist)<br>PowerShell<br>Microsoft Graph<br>Endbenutzerportal| Microsoft 365-Portal<br>Azure AD-Portal<br>PowerShell<br>Microsoft Graph<br>In Microsoft 365-Anwendungen |
| Von wem wird standardmäßig die Erstellung durchgeführt?| Administratoren <br>Benutzer| Administrators<br>Benutzer |
| Wer kann standardmäßig hinzugefügt werden?| Interne Benutzer*innen (Mandantenmitglieder)| Mandantenmitglieder und Gäste beliebiger Organisationen |
| Worauf wird Zugriff gewährt?| Nur auf Ressourcen, für die die Zuweisung durchgeführt wurde| Alle gruppenbezogenen Ressourcen:<br>(Gruppenpostfach, Website, Team, Chats und andere enthaltene Microsoft 365-Ressourcen)<br>Alle anderen Ressourcen, denen die Gruppe hinzugefügt wird |
| Nutzung möglich mit| Bedingter Zugriff<br>Berechtigungsverwaltung<br>Gruppenlizenzierung| Bedingter Zugriff<br>Berechtigungsverwaltung<br>Vertraulichkeitsbezeichnungen |



Verwenden Sie Microsoft 365-Gruppen zum Erstellen und Verwalten von bestimmten Microsoft 365-Ressourcen, z. B. eines Teams und der zugehörigen Websites und Inhalte. Die Gruppen stellen eine gute Wahl für einen projektbasierten Ansatz dar. 

 

## <a name="azure-ad-security-groups"></a>Azure AD-Sicherheitsgruppen 

[Azure AD-Sicherheitsgruppen](./active-directory-manage-groups.md) können Benutzer oder Geräte enthalten und zum Verwalten des Zugriffs auf folgende Komponenten genutzt werden: 

* Azure-Ressourcen, z. B. Microsoft 365-Apps, benutzerdefinierte Apps und SaaS-Apps (Software-as-a-Service) wie ServiceNow von Dropbox

* Azure-Daten und -Abonnements

* Azure-Dienste

Azure AD-Sicherheitsgruppen können auch für folgende Zwecke genutzt werden:

* Zuweisen von Lizenzen für Dienste wie Microsoft 365, Dynamics 365 und Enterprise Mobility + Security. Weitere Informationen finden Sie unter [Gruppenbasierte Lizenzierung](./active-directory-licensing-whatis-azure-portal.md).

* Zuweisen von erweiterten Berechtigungen. Weitere Informationen finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](../roles/groups-concept.md). 

Navigieren Sie zum Erstellen einer Gruppe [im Azure-Portal](./active-directory-groups-create-azure-portal.md) zu Azure Active Directory und dann zu „Gruppen“. Sie können Azure AD-Sicherheitsgruppen auch erstellen, indem Sie [PowerShell-Cmdlets](../enterprise-users/groups-settings-v2-cmdlets.md) verwenden. 

> [!NOTE]
> Eine Sicherheitsgruppe kann zum Zuweisen von maximal 1.500 Anwendungen verwendet werden. 

![Screenshot: Erstellen einer Sicherheitsgruppe](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Navigieren Sie zum Erstellen einer E-Mail-fähigen Sicherheitsgruppe zum [Microsoft 365 Admin Center](https://admin.microsoft.com/)** . Im Azure AD-Portal ist die Erstellung nicht möglich. 
<br>Bei der Erstellung müssen Sie eine Sicherheitsgruppe für E-Mails aktivieren. Zu einem späteren Zeitpunkt ist die Aktivierung nicht mehr möglich.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Hybridorganisationen und Azure AD-Sicherheitsgruppen

Hybridorganisationen verfügen sowohl über eine lokale Infrastruktur als auch über eine Azure AD-Cloudinfrastruktur. Viele Hybridorganisationen, die Active Directory nutzen, erstellen ihre Sicherheitsgruppen lokal und synchronisieren sie mit der Cloud. Bei dieser Methode können den Sicherheitsgruppen nur Benutzer aus der lokalen Umgebung hinzugefügt werden.

**Schützen Sie Ihre lokale Infrastruktur vor einer Kompromittierung, da über das Eindringen in die lokale Umgebung der Zugriff auf Ihren Microsoft 365-Mandanten möglich ist**. Eine entsprechende Anleitung finden Sie unter [Schützen von Microsoft 365 vor Angriffen auf die lokale Umgebung](./protect-m365-from-on-premises-attacks.md).

## <a name="microsoft-365-groups"></a>Microsoft 365-Gruppen

[Microsoft 365-Gruppen](/microsoft-365/admin/create-groups/office-365-groups) stellen den grundlegenden Mitgliedschaftsdienst für die Steuerung des gesamten Microsoft 365-Zugriffs dar. Diese Gruppen können im [Azure-Portal](https://portal.azure.com/) oder im [Microsoft 365-Portal](https://admin.microsoft.com/) erstellt werden. Wenn Sie eine Microsoft 365-Gruppe erstellen, gewähren Sie Zugriff auf eine Gruppe von Ressourcen, die für die Zusammenarbeit genutzt werden. Eine umfassende Liste dieser Ressourcen finden Sie unter [Übersicht über Microsoft 365-Gruppen für Administratoren](/microsoft-365/admin/create-groups/office-365-groups).

Für die Rollen von Microsoft 365-Gruppen gilt die folgende Unterteilung:

* **Besitzer:** Gruppenbesitzer*innen können Mitglieder hinzufügen oder entfernen und verfügen über spezifische Administratorrechte in der Gruppe, z. B. zum Löschen von Unterhaltungen aus dem freigegebenen Postfach oder zum Ändern von Gruppeneinstellungen. Gruppenbesitzer können die Gruppe umbenennen, die Beschreibung oder das Bild aktualisieren und weitere Aufgaben durchführen.

* **Mitglieder:** Gruppenmitglieder haben Zugriff auf alle Elemente der Gruppe, aber sie können keine Gruppeneinstellungen ändern. Standardmäßig können Gruppenmitglieder Gäste dazu einladen, ihrer Gruppe beizutreten. Sie können [diese Einstellung steuern](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Gäste**: Gäste von Gruppen sind Mitglieder, die keine internen Mitarbeiter Ihrer Organisation sind. Für Gäste sind einige Teams-Funktionen standardmäßig eingeschränkt.

 

### <a name="microsoft-365-group-settings"></a>Microsoft 365-Gruppeneinstellungen

Beim Einrichten wählen Sie den E-Mail-Alias und die Datenschutzeinstellungen aus und geben an, ob die Gruppe für Teams aktiviert werden soll. 

![Screenshot: Bearbeiten der Einstellungen von Microsoft 365-Gruppen](media/secure-external-access/4-edit-group-settings.png)

Nach Abschluss der Einrichtung können Sie Mitglieder hinzufügen, Einstellungen für die E-Mail-Nutzung konfigurieren usw.

### <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie Informationen zum Sichern des externen Zugriffs auf Ressourcen. Wir empfehlen Ihnen, die Aktionen in der angegebenen Reihenfolge durchzuführen.

1. [Ermitteln des gewünschten Sicherheitsstatus für externen Zugriff](1-secure-access-posture.md)

2. [Ermitteln des aktuellen Status](2-secure-access-current-state.md)

3. [Erstellen eines Governanceplans](3-secure-access-plan.md)

4. [Verwenden von Gruppen für Sicherheitszwecke](4-secure-access-groups.md) (dieser Artikel)

5. [Durchführen der Umstellung auf Azure AD B2B](5-secure-access-b2b.md)

6. [Schützen des Zugriffs mit der Berechtigungsverwaltung](6-secure-access-entitlement-managment.md)

7. [Schützen des Zugriffs mit Richtlinien für bedingten Zugriff](7-secure-access-conditional-access.md)

8. [Schützen des Zugriffs mit Vertraulichkeitsbezeichnungen](8-secure-access-sensitivity-labels.md)

9. [Schützen des Zugriffs auf Microsoft Teams, OneDrive und SharePoint](9-secure-access-teams-sharepoint.md)
